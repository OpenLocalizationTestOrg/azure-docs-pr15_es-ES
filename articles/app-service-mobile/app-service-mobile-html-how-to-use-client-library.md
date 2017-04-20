<properties
    pageTitle="Cómo usar el SDK de JavaScript para aplicaciones móviles de Azure"
    description="Cómo usar v para las aplicaciones móviles de Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Cómo usar la biblioteca de cliente de JavaScript para aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Esta guía le enseña a realizar escenarios comunes mediante el último [SDK de JavaScript para las aplicaciones móviles de Azure]. Si está acostumbrado a trabajar con aplicaciones de Azure Mobile, completar el [Inicio rápido de Azure Mobile aplicaciones] para crear un back-end y crear una tabla. En esta guía se centra en el uso el back-end móvil en aplicaciones Web HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas admitidas

Limitar la compatibilidad del explorador para las versiones actuales y el apellido de los exploradores principales: Google Chrome, Microsoft Edge, Microsoft Internet Explorer y Mozilla Firefox.  Esperamos que el SDK para funcionar con cualquier explorador relativamente moderno.

El paquete se distribuye como un módulo de JavaScript Universal, por lo que admite globales, AMD, y CommonJS formatos.

##<a name="Setup"></a>El programa de instalación y los requisitos previos

Esta guía asume que ha creado un back-end con una tabla. Esta guía se supone que la tabla tiene el mismo esquema que las tablas en los tutoriales.

Instalar el SDK de JavaScript de aplicaciones de Azure Mobile puede hacerse a través de la `npm` comando:

```
npm install azure-mobile-apps-client --save
```

Una vez instalado, la biblioteca se encuentra en `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`.  Copiar este archivo en el área de la web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

La biblioteca puede utilizarse también como un módulo ES2015, dentro de los entornos CommonJS como Browserify y Webpack y como una biblioteca AMD.  Por ejemplo:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Cómo: autenticar a los usuarios

Servicio de aplicación de Azure admite autenticación y autorización de los usuarios de la aplicación con varios proveedores de identidades externo: Facebook, Google, Account de Microsoft y Twitter. Puede establecer permisos en las tablas para restringir el acceso para operaciones específicas a solo los usuarios autenticados. También puede usar la identidad de los usuarios autenticados para implementar reglas de autorización de secuencias de comandos del servidor. Para obtener más información, consulte el tutorial de [Introducción a la autenticación] .

Se admiten dos flujos de autenticación: un flujo de servidor y un cliente.  El flujo de servidor proporciona la experiencia de autenticación más sencilla, como se basa en la interfaz del proveedor web autenticación. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo como única sesión tal como se basa en el SDK de específicas del proveedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Cómo: configurar el servicio móvil de aplicación para las direcciones URL de redirección externos.

Varios tipos de aplicaciones de JavaScript utilizan una capacidad de bucle para controlar los flujos de OAuth UI.  Estas capacidades incluyen:

* Ejecuta el servicio localmente
* Usar Live volver a cargar con el marco iónico
* Redirigir a la aplicación de servicio para la autenticación. 

Ejecuta de forma local puede causar problemas porque, de forma predeterminada, autenticación de servicio de aplicaciones sólo está configurada para permitir el acceso desde la aplicación móvil de back-end. Utilice los siguientes pasos para cambiar la configuración del servicio de aplicación para habilitar la autenticación cuando se ejecuta el servidor localmente:

1. Inicie sesión en el [portal de Azure]
2. Vaya a la aplicación móvil de back-end.
3. En el menú **Herramientas de desarrollo** , seleccione **Explorador de recursos** .
4. Haga clic en **Ir** para abrir el Explorador de recursos para la aplicación móvil de back-end en una nueva pestaña o ventana.
5. Expanda la **configuración**de > **authsettings** nodo de la aplicación.
6. Haga clic en el botón **Editar** para habilitar la edición del recurso.
7. Busque el elemento **allowedExternalRedirectUrls** , que debe ser null. Agregue las direcciones URL de una matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Reemplazar las direcciones URL de la matriz con las direcciones URL de su servicio, que en este ejemplo es `http://localhost:3000` para el servicio de ejemplo Node.js local. También puede usar `http://localhost:4400` para el servicio de onda u otra URL, dependiendo de la configuración de la aplicación.

8. En la parte superior de la página, haga clic en **De sólo lectura**, haga clic en **poner** para guardar las actualizaciones.

También debe agregar las mismas direcciones URL de bucle para la configuración de la lista blanca CORS:

1. Vaya al [portal de Azure].
2. Vaya a la aplicación móvil de back-end.
3. Haga clic en **CORS** en el menú de la **API** .
4. Escriba cada dirección URL en el cuadro de texto vacío **Permite orígenes** .  Se crea un nuevo cuadro de texto.
5. Haga clic en **Guardar**
    
Una vez que se actualice el back-end, podrá usar las nuevas direcciones URL de bucle en la aplicación.

<!-- URLs. -->
[Inicio rápido de aplicaciones móviles Azure]: app-service-mobile-cordova-get-started.md
[Introducción a la autenticación]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portal de Azure]: https://portal.azure.com/
[SDK de JavaScript para aplicaciones móviles de Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

