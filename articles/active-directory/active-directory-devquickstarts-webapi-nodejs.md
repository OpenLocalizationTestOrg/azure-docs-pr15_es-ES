<properties
    pageTitle="Introducción a Azure AD NodeJS | Microsoft Azure"
    description="Cómo crear Node.js REST Web API que se integra con Azure AD para la autenticación."
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="getting-started-with-web-api-for-node"></a>Introducción a WEB API de nodo

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

**Passport** es software intermedio de autenticación para Node.js. Muy flexible y modular, Passport puede ser discreta colocar cualquier base de Express o Resitify la aplicación web. Un conjunto de estrategias admite la autenticación con un nombre de usuario y contraseña, Facebook, Twitter y más. Hemos desarrollado una estrategia para Microsoft Azure Active Directory. Se instalará este módulo y, a continuación, agregar Microsoft Azure Active Directory `passport-azure-ad` complemento.

Para ello, necesitará:

1. Registrar una aplicación con Azure AD
2. Configurar la aplicación para usar azure ad-passport de Passport complemento.
3. Configurar una aplicación de cliente para llamar a la a hacer lista Web API

El código de este tutorial se mantiene [en GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [AZURE.NOTE] Este artículo no trata sobre cómo implementar el inicio de sesión, suscripción y administración de perfiles de Azure AD B2C.  Se centra en web llamada API después de que el usuario ya está autenticado.  Si todavía no lo ha hecho, debe empezar con [cómo integrar con documento de Azure Active Directory](./develop/active-directory-how-to-integrate.md) para obtener información sobre los conceptos básicos de Azure Active Directory.


Se ha publicado todo el código fuente para este ejemplo de ejecución de GitHub en una licencia MIT, así que no dude en clonar (o incluso mejor, horquilla!) y proporcionar comentarios y extraen solicitudes.

## <a name="about-nodejs-modules"></a>Acerca de los módulos Node.js

En este tutorial vamos a usar Node.js módulos. Módulos son puede cargables paquetes de JavaScript que proporcionan funcionalidad específica de la aplicación. Normalmente instalar los módulos mediante la herramienta de línea de comandos de Node.js NPM NPM directorio de instalación, pero se incluyen algunos módulos, como el módulo HTTP, el paquete de Node.js core.
Módulos instalados se guardan en el directorio node_modules en la raíz de su directorio de instalación Node.js. Cada módulo en el directorio node_modules mantiene su propio directorio node_modules que contiene todos los módulos depende, y cada módulo necesario tiene un directorio node_modules. Esta estructura de directorios recursiva representa la cadena de dependencia.

Resultados de la estructura de la cadena de dependencia en un espacio de aplicación más grande, pero garantiza que se cumplen todas las dependencias y la versión de los módulos de desarrollo también se utilizará en producción. Esto permite que el comportamiento de la aplicación de producción más predecible y evita problemas de control de versiones que pueden afectar a los usuarios.

## <a name="1-register-a-azure-ad-tenant"></a>1. registrar a un inquilino AD Azure

Para utilizar este ejemplo necesitará a un inquilino de Azure Active Directory. Si no está seguro de qué un inquilino es o cómo obtendría uno, consulte [cómo obtener un Azure AD de inquilinos](active-directory-howto-tenant.md).

## <a name="2-create-an-application"></a>2. crear una aplicación

Ahora debe crear una aplicación en el directorio, lo cual proporciona a Azure AD alguna información que necesita para comunicarse de forma segura con la aplicación.  La aplicación de cliente y la web API estará representados por una sola **ID de aplicación** en este caso, puesto que componen una aplicación de lógica.  Para crear una aplicación, siga [estas instrucciones](active-directory-how-applications-are-added.md). Si va a crear una aplicación de línea de negocio [estas instrucciones adicionales pueden ser útiles](active-directory-applications-guiding-developers-for-lob-applications.md).

Asegúrese de:

- Inicie sesión en el Portal de administración de Azure.
- En la barra de navegación izquierda, haga clic en **Active Directory**.
- Seleccione al inquilino donde desea registrar la aplicación.
- Haga clic en la pestaña de **aplicaciones** y haga clic en Agregar en la Bandeja de la parte inferior.
- Siga las indicaciones y cree una nueva **aplicación Web o WebAPI**.
    - El **nombre** de la aplicación describe la aplicación a los usuarios finales
    - La **Dirección URL de inicio de sesión** es la dirección URL base de la aplicación.  El valor predeterminado del código de ejemplo es `https://localhost:8080`.
    - La **Aplicación identificador URI** es un identificador único para la aplicación.  La convención consiste en usar `https://<tenant-domain>/<app-name>`, p. ej.`https://contoso.onmicrosoft.com/my-first-aad-app`
- Una vez haya completado el registro, AAD asignar su aplicación un identificador único del cliente.  Necesitará este valor en las siguientes secciones, así que cópielo desde la ficha configurar.

- Aviso: crear un **Secreto de aplicación** de la aplicación y copiar hacia abajo.  Se necesita breve.
- Aviso: Copia hacia abajo el **Identificador de la aplicación** que se asigna a la aplicación.  También necesitará breve.


## <a name="3-download-nodejs-for-your-platform"></a>3. descargar node.js para su plataforma
Para usar correctamente en este ejemplo, debe tener una instalación de trabajo de Node.js.

Instale Node.js desde [http://nodejs.org](http://nodejs.org).

## <a name="4-install-mongodb-on-to-your-platform"></a>4. instalar MongoDB a su plataforma

Para usar correctamente en este ejemplo, debe tener una instalación de trabajo de MongoDB. Vamos a utilizar MongoDB para realizar nuestra continua API de REST en varias instancias de servidor.

Instale MongoDB de [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Este tutorial se supone que usa los extremos de instalación y servidor predeterminado para MongoDB, que en el momento de escribir este artículo es: mongodb://localhost


## <a name="5-install-the-restify-modules-in-to-your-web-api"></a>5. instalar los módulos Restify a la API de Web

Vamos a usar Resitfy crear nuestra API de REST. Restify se deriva de un marco de la aplicación Node.js mínimo y flexible de Express que tiene un conjunto sólido de características para crear las API de REST sobre conectar.

### <a name="install-restify"></a>Instalar Restify

Desde la línea de comandos, cambie los directorios al directorio azuread. Si el directorio **azuread** no existe, créela.

`cd azuread - or- mkdir azuread; cd azuread`

Escriba el siguiente comando:

`npm install restify`

Este comando instala Restify.

#### <a name="did-you-get-an-error"></a>¿APARECE UN ERROR?

Cuando se usa npm en algunos sistemas operativos, puede recibir un error de Error: EPERM, chmod '/ usr/local/bin /...' y una solicitud de intente ejecutar como administrador de la cuenta. Si es así, utilice el comando de sudo para ejecutar npm en un nivel de privilegios superior.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>¿APARECE UN ERROR REFERENTE A DTRACE?

Puede ver algo parecido a esto, al instalar Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```


Restify proporciona un mecanismo eficaz para realizar el seguimiento de llamadas de resto con DTrace. Sin embargo, muchos sistemas operativos no tienen DTrace disponible. Puede omitir estos errores.


El resultado de este comando debería ser similar al siguiente:


    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)


## <a name="6-install-passportjs-in-to-your-web-api"></a>6. instalar Passport.js en su Web API

[Passport](http://passportjs.org/) es software intermedio de autenticación para Node.js. Muy flexible y modular, Passport puede ser discreta colocar cualquier base de Express o Resitify la aplicación web. Un conjunto de estrategias admite la autenticación con un nombre de usuario y contraseña, Facebook, Twitter y más. Hemos desarrollado una estrategia de Azure Active Directory. Se instalará este módulo y, a continuación, agregue la estrategia de Azure Active Directory complemento.

Desde la línea de comandos, cambie los directorios al directorio azuread.

Escriba el comando siguiente para instalar passport.js

`npm install passport`

El resultado del comando debería ser similar al siguiente:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="7-add-passport-azure-ad-to-your-web-api"></a>7. Agregue AD de Azure de Passport a su sitio Web API

A continuación, agregaremos la estrategia de OAuth con passport-azuread, un conjunto de estrategias que se conectan Azure Active Directory con Passport. Esta estrategia se utiliza para portador Tokens en este ejemplo de la API de Rest.

> [AZURE.NOTE] Aunque OAuth2 ofrece un marco en el que se puede emitir cualquier tipo de token conocido, solo determinados tipos de token han obtenido uso generalizado. Para proteger los extremos, que se ha convertido a ser portador Tokens. Tokens portador son más ampliamente emiten el tipo de token en OAuth2 y muchas implementaciones suponen que tokens portador están el único tipo de token emitido.

Desde la línea de comandos, cambie los directorios al directorio azuread

Escriba el comando siguiente para instalar el módulo de ad de azure de passport Passport.js:

`npm install passport-azure-ad`

El resultado del comando debería ser similar al siguiente:

    ``
    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## <a name="8-add-mongodb-modules-to-your-web-api"></a>8. Agregar módulos MongoDB a la API de Web

Vamos a usar MongoDB como nuestro almacén de datos por este motivo, es necesario instalar ambas la utilizado complemento para administrar modelos y esquemas llamado Mongoose, así como el controlador de base de datos para MongoDB, también se denomina MongoDB.


* `npm install mongoose`

## <a name="9--install-additional-modules"></a>9. instalar módulos adicionales

A continuación, se deberá instalar los módulos restantes necesarios.


Desde la línea de comandos, cambie los directorios a la carpeta **azuread** si no está allí:

`cd azuread`


Escriba los comandos siguientes para instalar los módulos siguientes en el directorio node_modules:

* `npm install assert-plus`
* `npm install bunyan`
* `npm update`


## <a name="10-create-a-serverjs-with-your-dependencies"></a>10. crear un server.js con las dependencias

El archivo server.js va a proporcionar la mayoría de la funcionalidad de nuestro servidor Web API. Se agregará la mayoría de nuestro código a este archivo. Fines de producción debería refactorizar la funcionalidad de los archivos más pequeños, como rutas independientes y controladores en. Con el fin de esta demostración usaremos server.js para esta funcionalidad.

Desde la línea de comandos, cambie los directorios a la carpeta **azuread** si no está allí:

`cd azuread`

Crear un `server.js` en nuestro editor favorito y agregue la información siguiente:

```Javascript
    'use strict';

    /**
    * Module dependencies.
    */

    var fs = require('fs');
    var path = require('path');
    var util = require('util');
    var assert = require('assert-plus');
    var bunyan = require('bunyan');
    var getopt = require('posix-getopt');
    var mongoose = require('mongoose/');
    var restify = require('restify');
    var passport = require('passport');
  var BearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Guarde el archivo. Se devolverá poco a él.

## <a name="11-create-a-config-file-to-store-your-azure-ad-settings"></a>11:. Crear un archivo de configuración para guardar la configuración de Azure AD

Este archivo de código pasa los parámetros de configuración de su Portal de Azure Active Directory a Passport.js. Cuando agrega la API de Web en el portal de la primera parte del tutorial creó estos valores de configuración. Explicaremos qué incluir en los valores de estos parámetros después de copiar el código.


Desde la línea de comandos, cambie los directorios a la carpeta **azuread** si no está allí:

`cd azuread`

Crear un `config.js` en nuestro editor favorito y agregue la información siguiente:

```Javascript
 exports.creds = {
     mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
     clientID: 'your client ID',
     audience: 'your application URL',
    // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
  // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
     identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
     validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
     passReqToCallback: false,
     loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

 };


```
Guarde el archivo.

## <a name="12-add-configuration-to-your-serverjs-file"></a>12. Agregar configuración al archivo server.js

Es necesario leer estos valores desde el archivo de configuración que acaba de crear en nuestra aplicación. Para ello, que simplemente agregue el archivo de config como un recurso necesario en la aplicación y, a continuación, establecer las variables globales con los valores en el documento config.js

Desde la línea de comandos, cambie los directorios a la carpeta **azuread** si no está allí:

`cd azuread`

Abrir su `server.js` en nuestro editor favorito y agregue la información siguiente:

```Javascript
var config = require('./config');
```
A continuación, agregue una nueva sección `server.js` con el siguiente código:

```Javascript
var options = {
    // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback,
    loggingLevel: config.creds.loggingLevel

};

// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;

// Our logger
var log = bunyan.createLogger({
    name: 'Azure Active Directory Bearer Sample',
         streams: [
        {
            stream: process.stderr,
            level: "error",
            name: "error"
        },
        {
            stream: process.stdout,
            level: "warn",
            name: "console"
        }, ]
});

  // if logging level specified, switch to it.
  if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
```

Guarde el archivo.



## <a name="13-add-the-mongodb-model-and-schema-information-using-moongoose"></a>13. agregar el modelo de MongoDB y la información del esquema mediante Moongoose

Ahora toda esta preparación se va a iniciar saldar nos parques juntos estos tres archivos en un servicio de la API de REST.

En este tutorial se utilizará MongoDB para almacenar las tareas tal como se describe en el ***paso 4***.

Si recuerda la `config.js` archivo que se creó en el ***paso 11*** llamamos a nuestra base de datos `tasklist` igual que lo que incluimos al final de la dirección URL de conexión de mogoose_auth_local. No es necesario crear esta base de datos con antelación en MongoDB, creará Esto nos en primera ejecución de la aplicación de servidor (suponiendo que aún no existe).

Ahora que hemos dicho al servidor de base de datos MongoDB nos gustaría usar, necesitamos escribir código adicional para crear el modelo y el esquema de tareas del servidor.

#### <a name="discussion-of-the-model"></a>Discusión del modelo

Nuestro modelo de esquema es muy sencillo y Expandir según sea necesario.

NOMBRE: el nombre de quién está asignado a la tarea. Una ***cadena***

TAREA - la tarea en Sí. Una ***cadena***

FECHA: la fecha en la que vence la tarea. ***Fecha y hora***

COMPLETADO: si la tarea se ha completado o no. Un ***valor BOOLEAN***

#### <a name="creating-the-schema-in-the-code"></a>Creación de esquemas en el código


Desde la línea de comandos, cambie los directorios a la carpeta **azuread** si no está allí:

`cd azuread`

Abrir su `server.js` en nuestro editor favorito y agregue la información siguiente debajo de la entrada de configuración:

```Javascript
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    task: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Como puede ver desde el código, creamos nuestro esquema y, a continuación, crear un objeto de modelo que se utilizará para almacenar los datos en todo el código cuando se definen nuestra ***rutas***.

## <a name="14-add-our-routes-for-our-task-rest-api-server"></a>14. Agregar nuestras rutas para el servidor de la API de REST de tarea

Ahora que tenemos un modelo de base de datos para trabajar con, vamos a agregar las rutas que se utilizará para el servidor de la API de REST.

### <a name="about-routes-in-restify"></a>Acerca de las rutas en Restify

Rutas de trabajar en Restify en la misma manera como lo hacen mediante la pila de Express. Definir rutas mediante el URI que esperan las aplicaciones cliente para llamar. Normalmente, se definen las rutas en un archivo independiente. Para nuestros fines, lo colocaremos nuestras rutas en el archivo server.js. Se recomienda que factor a su propio archivo de producción estos.

Es un patrón típico para una ruta de Restify:

```Javascript
function createObject(req, res, next) {

// do work on Object

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // keep the server going
}

....

server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.

```


Este es el patrón en el nivel más básico. Resitfy (y Express) proporcionan mucho más profunda functionaltiy como definir tipos de aplicaciones y realizar enrutamiento compleja entre extremos diferentes. Para nuestros fines, nos mantendremos estas rutas muy simple.

### <a name="1-add-default-routes-to-our-server"></a>1. agregar rutas predeterminadas a nuestro servidor

Ahora agregue las rutas CRUD básicas de crear, recuperar, actualizar y eliminar.

Desde la línea de comandos, cambie los directorios a la carpeta **azuread** si no está allí:

`cd azuread`

Abrir su `server.js` en nuestro editor favorito y agregue la información siguiente debajo de las entradas de la base de datos que ha especificado:

```Javascript

/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### <a name="2-next-lets-add-some-error-handling-in-our-apis"></a>2. a continuación, agregaremos algunos control de errores en las API:

```

///--- Errors for communicating something interesting back to the client

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="15-create-your-server"></a>¡15. crear su servidor!

Tenemos nuestro definido de la base de datos, tenemos nuestras rutas en lugar y el último que debe hacer es agregar nuestra instancia de servidor que administrará las llamadas.

Restify (y Express) tiene una gran cantidad de personalización en profundidad que puede hacer para un servidor de la API de REST, pero nuevamente usaremos la configuración básica para nuestros fines.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
```

## <a name="16-adding-the-routes-to-the-server-without-authentication-for-now"></a>16. agregar las rutas en el servidor (sin autenticación por ahora)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## <a name="17-before-we-add-oauth-support-lets-run-the-server"></a>17. antes de que se agrega la compatibilidad de OAuth, vamos a ejecutar el servidor.

Probar el servidor antes de que se agregue autenticación

La manera más sencilla de hacerlo es mediante doblez de una línea de comandos. Antes de hacerlo, tenemos una sencilla utilidad que nos permite analizar el resultado como JSON. Para ello, instale la herramienta de json todos los ejemplos siguientes usa.

`$npm install -g jsontool`

Instala la herramienta JSON globalmente. Ahora que nos hemos conseguir que, vamos a reproducir con el servidor:

En primer lugar, asegúrese de que se está ejecutando la instancia de monogoDB...

`$sudo mongod`

A continuación, cambie al directorio e iniciar patinaje...

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

A continuación, podemos agregar una tarea de esta manera:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

Debe ser la respuesta:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Y nos podemos lista de tareas para Brandon esta forma:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si todo esto funciona, estamos listos para agregar OAuth en el servidor de la API de REST.

**¡Tiene un servidor de la API de REST con MongoDB!**


## <a name="18-add-authentication-to-our-rest-api-server"></a>18. agregar autenticación para el servidor de la API de REST

Ahora que tenemos una ejecución API de REST (Enhorabuena, btw!) vamos a que le resulte útil contra Azure AD.

Desde la línea de comandos, cambie los directorios a la carpeta **azuread** si no está allí:

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1: usar la OIDCBearerStrategy que se incluye con ad de azure de passport

Hasta ahora hemos creado un servidor típico del resto TODO sin ningún tipo de autorización. Esto es donde se inicia reunir.

En primer lugar, debemos indicar que desea usar Passport. Poner este derecho después de la configuración del servidor:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Al escribir API siempre debe vincular los datos con un nombre único desde el token de que el usuario no puede imitar. Cuando el servidor almacena elementos TODO, almacena según el identificador de objeto del usuario en el token (llamado a través de token.oid) que se coloca en el campo "propietario". Esto garantiza que sólo ese usuario puede tener acceso a sus TODOs y nadie más puede tener acceso a la TODOs especificados. No hay ninguna exposición de la API de "propietario" para que un usuario externo puede solicitar TODOs de los demás, incluso si se autentican.

A continuación, vamos a utilizar la estrategia de portador que viene con ad de azure de passport. Solo tiene que buscar el código por ahora, voy a explicar en breve. Colocar esta después lo que pated anteriormente:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/

var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.sub === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var bearerStrategy = new BearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                users.push(token);
                owner = token.sub;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(bearerStrategy);
```

Passport utiliza un patrón similar para todos sus estrategias (Twitter, Facebook, etc.) que cumplen todos los autores de estrategia. En la estrategia ve que se pasa una function() que tiene un símbolo y listo como parámetros. La estrategia de forma leal volverá nos cuando lo haga todo lo del trabajo. Una vez que lo hace queremos almacenar el usuario y ocultar el token que no necesitamos pedir nuevamente.

> [AZURE.IMPORTANT]
El código anterior le lleva a cualquier usuario que pasa autenticar con el servidor. Esto se conoce como registro automático. En servidores de producción no desea permitir que todos los usuarios sin primero vaya a través de un proceso de registro que decida. Suele ser el modelo que ve en las aplicaciones de consumidor que le permiten registrar con Facebook pero pedirle que rellenar la información adicional. Si esto no era un programa de línea de comandos, nos podemos simplemente extraídos el correo electrónico del objeto token devuelto y, a continuación, se le solicite que rellenen información adicional. Puesto que se trata de un servidor de prueba le sumamos simplemente la base de datos en memoria.

### <a name="2-finally-protect-some-endpoints"></a>2. finalmente, proteger algunos extremos

Proteger extremos especificando la `passport.authenticate()` llamar con el protocolo que desea utilizar.

Vamos a editar nuestra ruta en nuestro código del servidor que hacer algo más interesante:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="19-run-your-server-application-again-and-ensure-it-rejects-you"></a>19. ejecute de nuevo la aplicación de servidor y asegúrese de rechaza

Vamos a usar `curl` nuevamente para ver si tenemos ahora OAuth2 protección contra nuestros extremos. Haremos esto antes está alguno de nuestro SDK de cliente con este extremo. Los encabezados devueltos deberían ser suficiente para decirnos que estamos hacia abajo de la trayectoria de la derecha.

En primer lugar, asegúrese de que la instancia de monogoDB está ejecutando:

  $sudo mongod

A continuación, cambie al directorio e iniciar patinaje...

  $ cd azuread $ nodo server.js

Pruebe una publicación básica:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Un 401 es la respuesta que busca, mientras que indica que la capa Passport está intentando redirigir al extremo autorizar, que es exactamente lo que desea.

## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>¡Felicidades! ¡Tiene un servicio de la API de REST mediante OAuth2!

Ha fue como puede hacerlo con este servidor sin usar un cliente de OAuth2 compatible. Debe ir a través de un tutorial adicional.

Si solo se busca información sobre cómo implementar una API de REST con Restify y OAuth2, tiene más de suficiente código para mantener desarrollar su servicio y aprender a crear en este ejemplo.

Si está interesado en los siguientes pasos en su viaje por la ADAL, estos son algunos clientes ADAL compatibles se recomienda para que continuar trabajando:

Simplemente clonar hacia abajo hasta el equipo del desarrollador y configurar como se indica en el tutorial.

[ADAL para iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
