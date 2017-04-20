###<a name="server-auth"></a>Cómo: autenticar con un proveedor (flujo de servidor)

Para aplicaciones móviles administrar el proceso de autenticación en su aplicación, debe registrar la aplicación con su proveedor de identidades. A continuación, en el servicio de aplicación de Azure, debe configurar el identificador de la aplicación y el secreto proporcionada por su proveedor.
Para obtener más información, consulte el tutorial [autenticación de agregar a su aplicación].

Una vez que haya registrado el proveedor de identidades, simplemente llame al método .login() con el nombre de su proveedor. Por ejemplo, para iniciar sesión con el uso de Facebook, el siguiente código.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Si está utilizando un proveedor de identidades que no sea de Facebook, cambie el valor pasado para el método de inicio de sesión anterior a uno de los siguientes: `microsoftaccount`, `facebook`, `twitter`, `google`, o `aad`.

En este caso, el servicio de aplicación de Azure administra el flujo de autenticación de OAuth 2.0 mostrando la página de inicio de sesión del proveedor seleccionado y generar un símbolo de autenticación del servicio de aplicación después de iniciar sesión correctamente con el proveedor de identidades. La función de inicio de sesión, cuando haya terminado, devuelve un objeto JSON (usuario) que expone el identificador de usuario y la aplicación de servicio token de autenticación en los campos de identificador de usuario y authenticationToken, respectivamente. Este token puede ser en caché y volver a utilizar hasta que caduca.

###<a name="client-auth"></a>Cómo: autenticar con un proveedor (flujo de cliente)

La aplicación puede también independientemente póngase en contacto con el proveedor de identidad y, a continuación, proporcionar el token devuelto en el servicio de aplicación para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o para recuperar datos de usuario adicionales desde el proveedor de identidades.

#### <a name="social-authentication-basic-example"></a>Ejemplo básico de autenticación social

Este ejemplo usa el SDK de cliente de Facebook para la autenticación:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
En este ejemplo se presupone que el token proporcionado por el proveedor respectivo SDK se almacena en la variable de token.

#### <a name="microsoft-account-example"></a>Ejemplo de Account de Microsoft

En el ejemplo siguiente se usa el SDK de Live, que es compatible con solo sesión para las aplicaciones de la tienda de Windows mediante Microsoft Account:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

En este ejemplo, se obtiene un token de Live conectar, que se suministra en el servicio de aplicación mediante una llamada a la función de inicio de sesión.

###<a name="auth-getinfo"></a>Cómo: obtener información sobre el usuario autenticado

Se puede recuperar la información de autenticación para el usuario actual de la `/.auth/me` extremo mediante cualquier método de AJAX.  Asegúrese de que el `X-ZUMO-AUTH` encabezado para el símbolo de autenticación.  El símbolo de autenticación se almacena en `client.currentUser.mobileServiceAuthenticationToken`.  Por ejemplo, para usar la API de búsqueda:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Capturar está disponible como un paquete de npm o explorador descargar desde CDNJS. También puede usar jQuery u otra API de AJAX para capturar la información.  Se recibirán datos como un objeto JSON.
