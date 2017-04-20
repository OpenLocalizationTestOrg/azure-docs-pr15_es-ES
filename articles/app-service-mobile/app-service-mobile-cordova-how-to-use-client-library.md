<properties
    pageTitle="Cómo usar Apache Cordova complemento para aplicaciones móviles de Azure"
    description="Cómo usar Apache Cordova complemento para aplicaciones móviles de Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Cómo usar la biblioteca de cliente de Cordova Apache para Azure aplicaciones móviles

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Esta guía le enseña a realizar escenarios comunes con la última [Apache Cordova complemento para las aplicaciones móviles de Azure]. Si está acostumbrado a trabajar con aplicaciones de Azure Mobile, primera completa [Inicio rápido de Azure Mobile aplicaciones] para crear un back-end, crear una tabla y descargar un proyecto de Apache Cordova predefinido. En esta guía se centrará en el complemento de cliente Apache Cordova.

## <a name="supported-platforms"></a>Plataformas admitidas

Este SDK es compatible con Apache Cordova v6.0.0 y versiones posteriores en iOS, Android y Windows dispositivos.  La compatibilidad de plataforma es la siguiente:

* API Android (KitKat a través de nueces) 19-24
* iOS 8.0 y versiones posteriores.
* Windows Phone 8.0
* Windows Phone 8.1
* Plataforma Windows universal

##<a name="Setup"></a>El programa de instalación y los requisitos previos

Esta guía asume que ha creado un back-end con una tabla. Esta guía se supone que la tabla tiene el mismo esquema que las tablas en los tutoriales. Esta guía también se supone que ha agregado el complemento de Cordova Apache al código.  Si aún no lo ha hecho, puede agregar el complemento Cordova Apache a su proyecto en la línea de comandos:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para obtener más información sobre cómo crear [su primera aplicación Apache Cordova], vea la documentación correspondiente.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="auth"></a>Cómo: autenticar a los usuarios

Servicio de aplicación de Azure admite autenticación y autorización de los usuarios de la aplicación con varios proveedores de identidades externo: Facebook, Google, Account de Microsoft y Twitter. Puede establecer permisos en las tablas para restringir el acceso para operaciones específicas a solo los usuarios autenticados. También puede usar la identidad de los usuarios autenticados para implementar reglas de autorización de secuencias de comandos del servidor. Para obtener más información, consulte el tutorial de [Introducción a la autenticación] .

Cuando utiliza la autenticación en una aplicación de Apache Cordova, los complementos de Cordova siguientes deben estar disponibles:

* [complemento de cordova de dispositivo]
* [complemento de cordova de inappbrowser]

Se admiten dos flujos de autenticación: un flujo de servidor y un cliente.  El flujo de servidor proporciona la experiencia de autenticación más sencilla, como se basa en la interfaz del proveedor web autenticación. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo como única sesión tal como se basa en el SDK de específicas del proveedor específica del dispositivo.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Cómo: configurar el servicio móvil de aplicación para las direcciones URL de redirección externos.

Varios tipos de aplicaciones de Apache Cordova utilizan una función de bucle para controlar flujos de OAuth UI.  Flujos de OAuth UI en el host local causar problemas, ya que el servicio de autenticación solo sabe cómo utilizar el servicio de forma predeterminada.  Ejemplos de flujos de OAuth UI problemáticos:

- Emulador de onda.
- Volver a cargar con iónico en directo.
- Ejecuta el back-end móvil localmente
- Ejecuta el back-end móvil en un servicio de aplicación de Azure diferente que la autenticación de proporcionar uno.

Siga estas instrucciones para agregar a la configuración de su configuración local:

1. Inicie sesión en el [portal de Azure]
2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de la aplicación móvil.
3. Haga clic en **Herramientas**
4. Haga clic en **Explorador de recursos** en el menú de respetar y luego haga clic en **Ir**.  Se abrirá una nueva ventana o pestaña.
5. Expanda la **configuración**, los nodos de **authsettings** para su sitio en el panel de navegación izquierdo.
6. Haga clic en **Editar**
7. Busque el elemento de "allowedExternalRedirectUrls".  Se puede establecer en null o una matriz de valores.  Cambie el valor en el siguiente valor:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Reemplazar las direcciones URL con las direcciones URL de su servicio.  Por ejemplo, "http://localhost:3000" (para el servicio de ejemplo Node.js) o "http://localhost:4400" (para el servicio de onda).  Sin embargo, estas direcciones URL son ejemplos - su situación, incluidos los servicios mencionados en los ejemplos, pueden ser diferentes.
8. Haga clic en el botón de **Lectura y escritura** en la esquina superior derecha de la pantalla.
9. Haga clic en el color verde botón **colocar** .

La configuración se guarda en este momento.  No se cierra la ventana del explorador hasta que haya terminado la configuración de guardar.
También puede agregar estas direcciones URL de bucle en la configuración de CORS de su aplicación de servicio:

1. Inicie sesión en el [portal de Azure]
2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de la aplicación móvil.
3. El módulo de configuración se abrirá automáticamente.  Si no aparece, haga clic en **Todas las opciones**.
4. Haga clic en **CORS** en el menú de la API.
5. Escriba la dirección URL que desea agregar en el cuadro proporcionado y presione ENTRAR.
6. Escriba las direcciones URL adicionales según sea necesario.
7. Haga clic en **Guardar** para guardar la configuración.

Tarda aproximadamente 10 y 15 segundos para la nueva configuración surta efecto.

##<a name="register-for-push"></a>Cómo: registrar las notificaciones de inserción

Instalar el [complemento de phonegap de inserción] para controlar las notificaciones de inserción.  Este complemento se puede agregar fácilmente con el `cordova plugin add` comando en la línea de comandos o mediante el programa de instalación del complemento Git dentro de Visual Studio.  El código siguiente en la aplicación de Apache Cordova registra el dispositivo para las notificaciones de inserción:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Use el SDK de Hubs de notificación para enviar notificaciones de inserción desde el servidor.  No enviar nunca notificaciones de inserción directamente desde clientes. Si lo hace, podría utilizarse para desencadenar un ataque de denegación de servicio notificación Hubs o la PNS.  La PNS puede prohibir el tráfico como resultado de estos ataques.

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com
[Inicio rápido de aplicaciones móviles Azure]: app-service-mobile-cordova-get-started.md
[Introducción a la autenticación]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Complemento de Cordova Apache para aplicaciones móviles de Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[la primera aplicación Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[complemento de phonegap de inserción]: https://www.npmjs.com/package/phonegap-plugin-push
[complemento de cordova de dispositivo]: https://www.npmjs.com/package/cordova-plugin-device
[complemento de cordova de inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
