<properties
    pageTitle="Aplicación Web de Azure AD versión 2.0 NodeJS | Microsoft Azure"
    description="Cómo crear una aplicación web de nodo JS que firma cuentas de trabajo o escuela y los usuarios con ambos Account personal de Microsoft."
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

# <a name="add-sign-in-to-a-nodejs-web-app"></a>Agregar inicio de sesión para una aplicación Web de nodeJS


> [AZURE.NOTE]
    No todas las características y escenarios de Azure Active Directory son compatibles con el extremo de la versión 2.0.  Para determinar si debe usar el extremo de la versión 2.0, lea acerca de [las limitaciones de la versión 2.0](active-directory-v2-limitations.md).


Aquí, usaremos Passport para:

- Inicie sesión el usuario en la aplicación con Azure AD y el extremo de la versión 2.0.
- Mostrar información sobre el usuario.
- Inicie sesión en el usuario de la aplicación.

**Passport** es software intermedio de autenticación para Node.js. Muy flexible y modular, Passport puede ser discreta colocar cualquier base de Express o Resitify la aplicación web. Un conjunto de estrategias admite la autenticación con un nombre de usuario y contraseña, Facebook, Twitter y más. Hemos desarrollado una estrategia para Microsoft Azure Active Directory. Se instalará este módulo y, a continuación, agregar Microsoft Azure Active Directory `passport-azure-ad` complemento.

## <a name="download"></a>Descargar

El código de este tutorial se mantiene [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs).  Para seguir a lo largo, puede [descargar el esquema de la aplicación como un .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) o clonar la estructura:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

La aplicación completada se proporciona al final de este tutorial también.

## <a name="1-register-an-app"></a>1. registrar una aplicación
Crear una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)o siga estos [pasos detallados](active-directory-v2-app-registration.md).  Asegúrese de que:

- Copia hacia abajo el **Identificador de la aplicación** asignado a la aplicación, tendrá pronto.
- Agregue la plataforma de **Web** para la aplicación.
- Escriba el correcto **URI redirigir**. La redirección URI indica a Azure AD donde se deben dirigir respuestas de autenticación: el valor predeterminado para este tutorial es `http://localhost:3000/auth/openid/return`.

## <a name="2-add-pre-requisities-to-your-directory"></a>2. Agregar pre-requisities al directorio

En la línea de comandos, cambie los directorios a la carpeta raíz si no está allí y ejecute los comandos siguientes:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- Además, hemos usar `passport-azure-ad` en la estructura del tutorial rápido.

- `npm install passport-azure-ad`


Se va a instalar las bibliotecas dependen de ese ad de azure passport.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. configurar la aplicación para usar la estrategia de nodo de passport de js
A continuación, se configurará el software Express intermedio para utilizar el protocolo de autenticación OpenID conectarse.  Passport se utilizará para emitir solicitudes de inicio de sesión y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

-   Para comenzar, abra la `config.js` de archivos en la raíz del proyecto y a continuación, especifique los valores de configuración de la aplicación en la `exports.creds` sección.
    -   La `clientID:` es el **Identificador de la aplicación** asignada a su aplicación en el portal de registro.
    -   La `returnURL` es la **Redirección URI** especificado en el portal.
    - La `clientSecret` es el secreto generado en el portal.

- Abra `app.js` en la raíz del proyecto y agregue la llamada siguiente para llamar a la `OIDCStrategy` estrategia que viene con`passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Después, use la estrategia que se acaba de referencia para controlar las solicitudes de inicio de sesión

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2)
//
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    skipUserProfile: config.creds.skipUserProfile
    scope: config.creds.scope
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport utiliza un patrón similar para todos sus estrategias (Twitter, Facebook, etc.) que cumplen todos los autores de estrategia. En la estrategia ve que se pasa una function() que tiene un símbolo y listo como parámetros. La estrategia de forma leal volverá nos cuando lo haga todo lo del trabajo. Una vez que lo hace queremos almacenar el usuario y ocultar el token que no necesitamos pedir nuevamente.

> [AZURE.IMPORTANT]
El código anterior le lleva a cualquier usuario que pasa autenticar con el servidor. Esto se conoce como registro automático. En servidores de producción no desea permitir que todos los usuarios sin primero vaya a través de un proceso de registro que decida. Suele ser el modelo que ve en las aplicaciones de consumidor que le permiten registrar con Facebook pero pedirle que rellenar la información adicional. Si esto no era una aplicación de ejemplo, hemos podríamos simplemente extraídos el correo electrónico desde el objeto de token que se devuelve y, a continuación, se le solicite que rellenen información adicional. Puesto que se trata de un servidor de prueba le sumamos simplemente la base de datos en memoria.

- A continuación, vamos a agregar los métodos que nos permitirá realizar un seguimiento del inicio de sesión de los usuarios según sea necesario por Passport. Esto incluye serializar y deserializar la información del usuario:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- A continuación, vamos a agregar el código para cargar el motor express. Aquí verá usamos /views de forma predeterminada y proporciona el modelo de /routes Express.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Por último, vamos a agregar las rutas de publicación que se entregan las solicitudes de inicio de sesión real para la `passport-azure-ad` motor:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. utilizar Passport a emitir solicitudes de inicio de sesión y cierre de sesión a Azure AD

La aplicación ahora está configurada correctamente para comunicarse con el punto final de la versión 2.0 mediante el protocolo de autenticación OpenID conectarse.  `passport-azure-ad`se encargan de todos los detalles horrible de elaborar los mensajes de autenticación, validación tokens de Azure AD y mantener la sesión de usuario.  Todo lo que queda ofrecen a los usuarios un modo de inicio de sesión, cerrar sesión y recopilar información adicional sobre el usuario que inició sesión.

- En primer lugar, le permite agregar los métodos de forma predeterminada, inicio de sesión, cuenta y cierre de sesión a nuestro `app.js` archivo:

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   Vamos a revisar en detalle:
    -   La `/` ruta le redirigirá a la vista de index.ejs pasando el usuario en la solicitud (si existe)
    - La `/account` ruta le primero ***Asegúrese de que estamos autenticados*** (se implementar que debajo) y, a continuación, pase el usuario en la solicitud para que podamos obtener información adicional sobre el usuario.
    - La `/login` ruta llamará a nuestro autenticador azuread openidconnect de `passport-azuread` y si no son correctas will redirigen al usuario a /login
    - La `/logout` simplemente llamará la logout.ejs (y distribuir) que borra las cookies y, a continuación, devolver el usuario index.ejs


- En la última parte de `app.js`, vamos a agregar el método EnsureAuthenticated que se usa en `/account` encima.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- Por último, vamos a realmente crear el mismo servidor en `app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5. crear las vistas y las rutas en express para mostrar nuestro usuario en el sitio Web

Tenemos nuestro `app.js` completa. Ahora necesitamos agregar las rutas y las vistas que mostrará la información que tenemos que el usuario, así como controlador de la `/logout` y `/login` rutas que hemos creado.

- Crear la `/routes/index.js` ruta bajo el directorio raíz.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Crear la `/routes/user.js` ruta bajo el directorio raíz

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Estas rutas simples solo pasará a lo largo de la solicitud a nuestras vistas, incluido el usuario si está presente.

- Crear la `/views/index.ejs` vista en el directorio raíz. se trata de una página sencilla que llamará nuestros métodos de inicio de sesión y cierre de sesión y permitir que nos captar la información de cuenta. Observe que podemos utilizar el condicional `if (!user)` como el usuario que se pasa a través de la solicitud es pruebas tenemos iniciado en el usuario.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- Crear la `/views/account.ejs` ver en el directorio raíz de modo que podemos ver información adicional que `passport-azuread` ha incluido en la solicitud del usuario.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Por último, vamos a hacer este aspecto bastante agregando un diseño. Crear la ' o vista de views/layout.ejs en el directorio raíz

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> |
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> |
            <a href="/account">Account</a> |
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

Por último, crear y ejecutar la aplicación!

Ejecutar `node app.js` y vaya a`http://localhost:3000`


Inicie sesión con una Account personal de Microsoft o una cuenta profesional o educativa y observe cómo la identidad del usuario se refleja en la lista de Account.  Ahora tiene una aplicación web segura mediante protocolos estándar que pueden autenticar a los usuarios con sus cuentas personales y de trabajo o la escuela.

##<a name="next-steps"></a>Pasos siguientes

Para referencia, el ejemplo completo (sin los valores de configuración) [se proporciona como un .zip aquí](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)o puede clonar ella desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Ahora puede desplazarse a temas más avanzados.  Desea probar:

[Proteger una node.js web api utilizando el extremo de la versión 2.0 >>](active-directory-v2-devquickstarts-node-api.md)

Para obtener recursos adicionales, consulte:
- [La Guía del programador de versión 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Etiqueta de StackOverflow "azure-active directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtener actualizaciones de seguridad de nuestros productos

Le recomendamos que obtener notificaciones de cuándo se producen incidencias de seguridad, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscribirse a las alertas de aviso de seguridad.
