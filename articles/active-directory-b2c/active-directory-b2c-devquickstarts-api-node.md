<properties
    pageTitle="B2C de Azure AD: Proteger un web API mediante Node.js | Microsoft Azure"
    description="Cómo crear un sitio web de Node.js API que acepte tokens desde un inquilino B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="08/30/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>B2C de Azure AD: Proteger un web API mediante Node.js

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con B2C de Azure Active Directory (AD Azure), puede proteger un web API mediante tokens de acceso OAuth 2.0. Estos tokens permitir que las aplicaciones cliente que utilizan Azure AD B2C autenticar a la API. Este artículo le muestra cómo crear una API de "lista de tareas pendientes" que permite a los usuarios agregar y lista de tareas. La web API está protegida mediante Azure AD B2C y solo permite a los usuarios administrar su lista de tareas pendientes autenticados.

> [AZURE.NOTE]  Este ejemplo se ha escrito a estar conectado a usando nuestra [iOS B2C aplicación de ejemplo](active-directory-b2c-devquickstarts-ios.md). Hacer el análisis actual en primer lugar y siga junto con esa muestra.

**Passport** es software intermedio de autenticación para Node.js. Flexible y modular, Passport puede instalarse discreta en cualquier basado en Express o Restify la aplicación web. Un conjunto de estrategias es compatible con la autenticación con un nombre de usuario y contraseña, Facebook, Twitter y más. Hemos desarrollado una estrategia de Azure Active Directory (AD Azure). Instalar este módulo y, a continuación, agregar Azure AD `passport-azure-ad` complemento.

Para realizar este ejemplo, necesita:

1. Registrar una aplicación con Azure AD.
2. Configurar la aplicación para utilizar de Passport `azure-ad-passport` complemento.
3. Configurar una aplicación de cliente para llamar a la API de web "lista de tareas pendientes".


## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C

Antes de poder usar Azure AD B2C, debe crear un directorio o de inquilinos.  Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos y mucho más.  Si no tiene uno ya, [Cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Crear una aplicación

A continuación, deberá crear una aplicación en el directorio B2C que ofrece información que necesita para comunicarse de forma segura con la aplicación de Azure AD. En este caso, la aplicación de cliente y la web API están representados por un solo **Identificador de la aplicación**, debido a que incluyen una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir un **web app/web api** en la aplicación
- Escriba `http://localhost/TodoListService` como una **dirección URL de respuesta**. Es la dirección URL predeterminada para este código de ejemplo.
- Crear un **secreto de aplicación** de la aplicación y copiarlo. Necesitará estos datos más adelante. Tenga en cuenta que este valor debe ser [un carácter de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de usarla.
- Copie el **Identificador de la aplicación** que se asigna a la aplicación. Necesitará estos datos más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define por una [Directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene dos experiencias de identidad: inicio de sesión e inicie sesión. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Cuando crea su tres directivas, asegúrese de:

- Elija el **nombre para mostrar** y otros atributos de la suscripción en la directiva de suscripción.
- Elija el **nombre para mostrar** y el **Identificador de objeto de** notificaciones de la aplicación en cada directiva.  Puede elegir otras reclamaciones también.
- Copiar el **nombre** de cada directiva después de crearlo. Debe tener el prefijo `b2c_1_`.  Necesitará más adelante esos nombres de directiva.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de haber creado las tres directivas, está listo para crear la aplicación.

Para obtener información sobre cómo funcionan las directivas de Azure AD B2C, comience con la [.NET web app tutorial de introducción](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Descargar el código

El código de este tutorial [se mantiene según GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Para generar el ejemplo mientras avanza, puede [descargar un proyecto de esquema como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). También puede clonar la estructura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

La aplicación completada también está [disponible como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) o en la `complete` rama del mismo repositorio.

## <a name="download-nodejs-for-your-platform"></a>Descargar Node.js para su plataforma

Para usar correctamente en este ejemplo, necesita una instalación de trabajo de Node.js. 

Instale Node.js desde [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Instalar MongoDB para su plataforma

Para usar correctamente en este ejemplo, necesita una instalación de trabajo de MongoDB. Usamos MongoDB para hacer que la API de REST persistente en varias instancias de servidor.

Instale MongoDB de [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Este tutorial se supone que usa los extremos de instalación y servidor predeterminado para MongoDB, que en el momento de escribir este artículo es `mongodb://localhost`.

## <a name="install-the-restify-modules-in-your-web-api"></a>Instalar los módulos Restify en su web API

Usamos Restify para generar la API de REST. Restify se deriva de un marco de la aplicación Node.js mínimo y flexible de Express. Tiene un conjunto sólido de características para crear las API de REST encima de conectarse.

### <a name="install-restify"></a>Instalar Restify

Desde la línea de comandos, cambie el directorio a `azuread`. Si el `azuread` directorio no existe, crearlo.

`cd azuread`o`mkdir azuread;`

Escriba el siguiente comando:

`npm install restify`

Este comando instala Restify.

#### <a name="did-you-get-an-error"></a>¿Aparece un error?

En algunos sistemas operativos, al usar `npm`, puede recibir el error `Error: EPERM, chmod '/usr/local/bin/..'` y una solicitud de ejecutar la cuenta como administrador. Si se produce este problema, use la `sudo` comando para ejecutar `npm` de un nivel de privilegios superior.

#### <a name="did-you-get-a-dtrace-error"></a>¿Aparece un error de DTrace?

Puede ver algo parecido a este texto cuando se instala Restify:

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

Restify proporciona un mecanismo eficaz para el resto de seguimiento llamadas usando DTrace. Sin embargo, muchos sistemas operativos no tienen DTrace disponible. Puede omitir estos errores.

El resultado del comando debería ser similar a este texto:

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

## <a name="install-passport-in-your-web-api"></a>Instalar Passport en su web API

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está allí.

Instalar Passport mediante el siguiente comando:

`npm install passport`

El resultado del comando debe ser similar a este texto:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Agregar passport azuread a la API de web

A continuación, agregue la estrategia de OAuth utilizando `passport-azuread`, un conjunto de estrategias que se conectan Azure AD con Passport. Use esta estrategia de token portador de la muestra de la API de REST.

> [AZURE.NOTE] Aunque OAuth2 ofrece un marco en el que se puede emitir cualquier tipo de token conocido, solo determinados tipos de token han obtenido uso extendido. Los tokens para proteger los extremos son tokens portador. Estos tipos de tokens son más ampliamente emitido en OAuth2. Muchas implementaciones suponga que tokens portador están el único tipo de token emitido.

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está allí.

Instalar el vínculo `passport-azure-ad` módulo mediante el siguiente comando:

`npm install passport-azure-ad`

El resultado del comando debe ser similar a este texto:

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
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Agregar módulos MongoDB a la API de web

Este ejemplo usa MongoDB como almacén de datos. Para que instale Mongoose, ampliamente utilizado complemento para administrar modelos y esquemas.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Instalar módulos adicionales

A continuación, instale los módulos restantes necesarios.

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está:

`cd azuread`

Instalar los módulos en su `node_modules` directorio:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`


## <a name="create-a-serverjs-file-with-your-dependencies"></a>Crear un archivo server.js con las dependencias

La `server.js` archivo proporciona la mayoría de la funcionalidad de su servidor de la API de Web. 

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está:

`cd azuread`

Crear un `server.js` archivo en un editor. Agregue la información siguiente:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Guarde el archivo. Volver a él más adelante.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Crear un archivo de config.js para almacenar la configuración de Azure AD

Este archivo de código pasa los parámetros de configuración de su Portal de Azure AD a la `Passport.js` archivo. Crear estos valores de configuración cuando agrega la API de web en el portal de la primera parte de la tutorial. Se explica qué incluir en los valores de estos parámetros después de copiar el código.

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está:

`cd azuread`

Crear un `config.js` archivo en un editor. Agregue la información siguiente:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>', 
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valores requeridos

`clientID`: El identificador de cliente de la aplicación Web API.

`IdentityMetadata`: Aquí es donde `passport-azure-ad` busca los datos de configuración para el proveedor de identidades. También busca las claves validar los símbolos de web JSON. 

`audience`: El identificador uniforme de recursos (URI) desde el portal que identifica la aplicación de llamada. 

`tenantName`: El nombre del inquilino (por ejemplo, **contoso.onmicrosoft.com**).

`policyName`: La directiva que desee validar los tokens que llegan al servidor. Esta directiva debe ser la misma directiva que usa para iniciar sesión en la aplicación cliente.

> [AZURE.NOTE] Para nuestra preview B2C, use las mismas directivas a través de la configuración de cliente y el servidor. Si ya ha completado un tutorial y ha creado estas directivas, no es necesario que vuelva a hacerlo. Puesto que finalizado el tutorial, no es necesario configurar directivas de nuevas para los tutoriales de cliente en el sitio.

## <a name="add-configuration-to-your-serverjs-file"></a>Agregar configuración al archivo server.js

Para leer los valores de la `config.js` que haya creado, agregue el `.config` archivo como un recurso necesario en la aplicación y, a continuación, establezca las variables globales a los de la `config.js` documento.

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está:

`cd azuread`

Abrir la `server.js` archivo en un editor. Agregue la información siguiente:

```Javascript
var config = require('./config');
```
Agregar una nueva sección `server.js` que incluye el siguiente código:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

A continuación, vamos a agregar marcadores de posición para los usuarios que recibimos de nuestras aplicaciones llamadas.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Continuemos y crear nuestra registrador demasiado.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Agregar la información de esquemas y modelo de MongoDB mediante Mongoose

La preparación anteriores compensa como poner estos tres archivos juntos en un servicio de la API de REST.

Para este tutorial, use MongoDB para almacenar las tareas, como se indicó anteriormente.

En la `config.js` archivo, se llama a su **lista de tareas**de base de datos. Ese nombre también era ponga al final de la `mongoose_auth_local` dirección URL de conexión. No es necesario crear esta base de datos con antelación en MongoDB. Crea la base de datos en la primera ejecución de la aplicación de servidor.

Después de indicar al servidor de base de datos MongoDB para usar, debe escribir código para crear el modelo y el esquema de las tareas de servidor adicional.

### <a name="expand-the-model"></a>Expanda el modelo

En este modelo de esquema es sencillo. Se puede expandir según sea necesario.

`owner`: Quién está asignado a la tarea. Este objeto es una **cadena**.  

`Text`: La tarea en Sí. Este objeto es una **cadena**.

`date`: La fecha en la que vence la tarea. Este objeto es una **fecha y hora**.

`completed`: Si la tarea está completada. Este objeto es **booleano**.

### <a name="create-the-schema-in-the-code"></a>Crear el esquema en el código

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está:

`cd azuread`

Abrir la `server.js` archivo en un editor. Agregue la información siguiente debajo de la entrada de configuración:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Primero crear el esquema y, a continuación, se crea un objeto de modelo que usar para almacenar los datos en todo el código al definir las **rutas**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Agregar rutas de su servidor de tarea de la API de REST

Ahora que ya tiene un modelo de base de datos para trabajar con, agregue las rutas que usa para el servidor de la API de REST.

### <a name="about-routes-in-restify"></a>Acerca de las rutas en Restify

Rutas de trabajar en Restify en la misma manera que funcionan al usar la pila de Express. Definir rutas utilizando el URI que esperan las aplicaciones cliente para llamar. 

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

Restify y Express puede proporcionar cuánta funcionalidad más profunda, como definir tipos de aplicaciones y realizar enrutamiento compleja entre extremos diferentes. Para el propósito de este tutorial, mantendremos estas rutas simple.

#### <a name="add-default-routes-to-your-server"></a>Agregar rutas predeterminadas en el servidor

Ahora agregue las rutas CRUD básicas de **crear** y **lista** para nuestra API de REST. Pueden encontrar otras rutas en la `complete` rama de la muestra.

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está:

`cd azuread`

Abrir la `server.js` archivo en un editor. A continuación, las entradas de la base de datos que ha especificado agregue la información siguiente:

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

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
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
```

```Javascript
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

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
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


#### <a name="add-error-handling-for-the-routes"></a>Agregar control de errores para las rutas

Agregar algún control de errores para que puedan comunicarse los problemas que encontrar al cliente de forma que puede comprender.

Agregue el código siguiente:

```Javascript
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


## <a name="create-your-server"></a>Crear el servidor

Ahora tiene definidas por la base de datos y poner las rutas en su lugar. Lo último que tiene que hacer es agregar la instancia del servidor que administra las llamadas.

Restify y Express proporcionar una personalización en profundidad para un servidor de la API de REST, pero usamos la configuración básica más aquí. 

```Javascript

**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
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
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Agregue las rutas en el servidor (sin autenticación)

```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

``` 

## <a name="add-authentication-to-your-rest-api-server"></a>Agregue autenticación al servidor API de REST

Ahora que ya tiene un servidor de la API de REST ejecución, puede hacerlo útiles contra Azure AD.

Desde la línea de comandos, cambie el directorio a `azuread`, si aún no está:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Usar el OIDCBearerStrategy que se incluye con ad de azure de passport


> [AZURE.TIP]
Cuando escribe API, siempre debe vincular los datos con un nombre único desde el token de que el usuario no puede imitar. Cuando el servidor almacena elementos ToDo, lo hace así basándose en el **oid** del usuario en el token (llamado a través de token.oid), que se envía en el campo "propietario". Este valor garantiza que sólo ese usuario puede tener acceso a sus propios elementos ToDo. No hay ninguna exposición de la API de "propietario", para que un usuario externo puede solicitar otros elementos ToDo, incluso si se autentican.

A continuación, utilice la estrategia de portador que viene con `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport utiliza el mismo patrón para todas sus estrategias. Pase un `function()` que tiene `token` y `done` como parámetros. La estrategia se suministra a usted una vez actualizada de su trabajo. A continuación, debe almacenar el usuario y guarde el token para que no es necesario volver a preguntar para el mismo.

> [AZURE.IMPORTANT]
El código anterior le lleva a todos los usuarios que pasa autenticar con el servidor. Este proceso se conoce como autoregistration. En servidores de producción, no permita que en cualquier acceso a los usuarios la API sin tener primero vaya a través de un proceso de registro. Este proceso suele ser el modelo que ve en las aplicaciones de consumidor que le permiten registrar, con Facebook pero pedirle que rellenar la información adicional. Si este programa no era un programa de línea de comandos, nos podríamos extraídos el correo electrónico desde el objeto de token que se devuelve y, a continuación, solicita a los usuarios rellenar información adicional. Dado que se trata de una muestra, se agregue a una base de datos en la memoria.



## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Ejecutar la aplicación de servidor para comprobar que se rechaza

Puede usar `curl` para ver si tiene ahora OAuth2 protección contra los extremos. Los encabezados devueltos deberían ser suficiente para indicar que se encuentra en la ruta de acceso correcta.

Asegúrese de que la instancia de MongoDB está ejecutando:

    $sudo mongodb

Cambie al directorio y ejecute el servidor:

    $ cd azuread
    $ node server.js

En una nueva ventana terminal, ejecutar`curl`

Pruebe una publicación básica:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Un error 401 es la respuesta que desea. Indica que la capa Passport está intentando redirigir al extremo autorizar.


## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Ahora tiene un servicio de la API de REST que usa OAuth2

¡Ha implementado una API de REST mediante Restify y OAuth! Ahora tiene suficiente código para que puedan continuar desarrollar el servicio y generar en este ejemplo. Han pasado como puede hacerlo con este servidor sin usar a un cliente compatible con el OAuth2. Para ese paso use un tutorial adicional como nuestro tutorial de [conectarse a un sitio web API usando iOS con B2C](active-directory-b2c-devquickstarts-ios.md) .


## <a name="next-steps"></a>Pasos siguientes

Ahora puede mover temas más avanzados, como por ejemplo:

[Conectarse a un sitio web API con iOS con B2C](active-directory-b2c-devquickstarts-ios.md)