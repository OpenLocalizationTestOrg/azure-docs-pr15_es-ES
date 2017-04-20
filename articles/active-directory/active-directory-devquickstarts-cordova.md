<properties
    pageTitle="Introducción a Azure AD Cordova | Microsoft Azure"
    description="Cómo crear una aplicación de Cordova que se integra con Azure AD para iniciar sesión y llamadas de Azure AD protegido API con OAuth."
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="vittorib"/>

# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Integrar Azure AD con un Apache Cordova aplicación

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova permite desarrollar aplicaciones HTML5 y JavaScript que se pueden ejecutar en dispositivos móviles como aplicaciones nativas completas.
Con Azure AD, puede agregar capacidades de autenticación de calificación de empresa a sus aplicaciones de Cordova. Gracias a un complemento de Cordova ajuste nativa SDK de Azure AD en iOS, Android, tienda de Windows y Windows Phone, puede mejorar su aplicación para admitir inicio de sesión en con cuentas de AD de los usuarios, acceder a Office 365 y las API de Azure y proteger incluso las llamadas a su propia API de Web personalizada.

En este tutorial se utilizará el complemento Cordova Apache para biblioteca de activos de autenticación de directorio (ADAL) para mejorar una aplicación simple con las características siguientes:

-   Con pocas líneas de código, autenticar a un usuario de AD y obtener un token para llamar a la API de Azure AD Graph.
-   Utilizar ese token para invocar la API de gráfico para ese directorio de la consulta y mostrar los resultados  
-   Aproveche la caché del símbolo ADAL para minimizar las peticiones de autenticación para el usuario.

Para ello, necesitará:

2. Registrar una aplicación con Azure AD
2. Agregar código a su aplicación de solicitud tokens
3. Agregar código para usar el símbolo para consultar la API de gráfico y mostrar los resultados.
4. Crear el proyecto de implementación de Cordova con todas las plataformas de que destino desea y a continuación, el complemento Cordova ADAL y pruebe la solución en emuladores.

## <a name="0--prerequisites"></a>*0. requisitos previos de*

Para completar este tutorial que debe:

- Un inquilino de Azure AD donde tiene una cuenta con derechos de desarrollo de aplicaciones
- Un entorno de desarrollo configurado para utilizar Apache Cordova  

Si ya tiene una configuración, vaya directamente al paso 1.

Si no tiene un inquilino de Azure AD, puede encontrar [instrucciones sobre cómo obtener una aquí](active-directory-howto-tenant.md).

Si no tiene Cordova Apache configurado en su equipo, instale lo siguiente:

- [GIT](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [CLI Cordova](https://cordova.apache.org/) (puede instalarse con facilidad a través del Administrador de paquetes NPM: `npm install -g cordova`)

Tenga en cuenta que los que deberían funcionar en su PC y Mac

Cada plataforma de destino tiene requisitos previos diferentes.

- Para generar y ejecutar la versión de la aplicación de teléfono o Tabletas de Windows
    - [2013 de Visual Studio para Windows Update 2 o posterior](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express u otra versión).
- Para crear y ejecutar para iOS
    -   Xcode 5.x o posterior. Descargar en http://developer.apple.com/downloads o en el [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
    -   [ios sim](https://www.npmjs.org/package/ios-sim) : le permite iniciar aplicaciones de iOS en iOS Simulator desde la línea de comandos (se puede instalar fácilmente a través de la terminal: `npm install -g ios-sim`)

- Para crear y ejecutar la aplicación para Android
    - Instalar el [Kit de desarrollo de Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o posterior. Asegúrese de que `JAVA_HOME` (Variable de entorno) está correctamente establecida según la ruta de instalación de JDK (por ejemplo, C:\Program Files\Java\jdk1.7.0_75).
    - Instalar [SDK Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) y agregar `<android-sdk-location>\tools` ubicación (por ejemplo, C:\tools\Android\android-sdk\tools) para su `PATH` variables de entorno.
    - Abrir el Administrador de SDK Android (por ejemplo, a través de terminal: `android`) e instalar
    - SDK de la plataforma *5.0.1 Android (API 21)*
    - *Herramientas de generación de android SDK* versión 19.1.0 o superior
    - *Repositorio de soporte técnico de Android* (Adicionales)

  Android sdk no proporciona ninguna instancia de emulador predeterminada. Crear una nueva ejecutando `android avd` de terminal y seleccionando *crear...* si desea ejecutar aplicación Android emulador. *Nivel de la Api* de recomendado es 19 o superior, vea [AVD Manager] (http://developer.android.com/tools/help/avd-manager.html) para obtener más información acerca de las opciones de creación y emulador Android.


## <a name="1--register-an-application-with-azure-ad"></a>*1. registrar una aplicación con Azure AD*

Nota: este __paso es opcional__. El tutorial proporciona valores configurados previamente que le permitirá ver en acción sin hacer cualquier aprovisionamiento en su propia inquilino. Sin embargo se recomienda que realizar este paso y familiarizarse con el proceso, como será necesario cuando se creen sus propias aplicaciones.

Azure AD tema tokens a aplicaciones conocidas. Antes de poder usar Azure AD desde la aplicación, debe crear una entrada para él en su inquilino.  Para registrar una nueva aplicación en su inquilino

-   Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com)
-   En la barra de navegación izquierda, haga clic en **Active Directory**
-   Seleccione al inquilino donde desea registrar la aplicación.
-   Haga clic en la pestaña de **aplicaciones** y haga clic en **Agregar** en la Bandeja de la parte inferior.
-   Siga las indicaciones y cree una nueva **Aplicación nativa de cliente** (a pesar de que Cordova aplicaciones están basadas en HTML, estamos creando así que aquí la aplicación cliente nativo `Native Client Application` opción debe estar seleccionada; en caso contrario, no funcionará la aplicación).
    -   El **nombre** de la aplicación describe la aplicación a los usuarios finales
    -   La **Redirección URI** es el URI utilizado para devolver los símbolos a su aplicación. Escriba `http://MyDirectorySearcherApp`.

Una vez haya completado el registro, AAD asignar su aplicación un identificador único del cliente.  Necesitará este valor en las siguientes secciones: puede encontrar en la ficha **Configurar** de la aplicación recién creada.

Para poder ejecutar `DirSearchClient Sample`, conceder el permiso de aplicación recién creado para la _API de Azure AD gráfico_de la consulta:
-   En la ficha **Configurar** , busque la sección "Permisos a otras aplicaciones".  Para la aplicación "Azure Active Directory", agregue el permiso de **acceso del directorio como el usuario que ha iniciado sesión** en **Permisos de delegado**.  Esto le permitirá la aplicación de la API de gráfico para los usuarios de la consulta.

## <a name="2-clone-the-sample-app-repository-required-for-the-tutorial"></a>*2. clonar el repositorio de aplicación de ejemplo necesario para el tutorial*

En el shell o la línea de comandos, escriba el siguiente comando:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="3-create-the-cordova-app"></a>*3. crear la aplicación de Cordova*

Hay varias formas de crear aplicaciones Cordova. En este tutorial se utilizará la interfaz de línea de comandos de Cordova (CLI).
En el shell o la línea de comandos, escriba el siguiente comando:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Que cree la estructura de carpetas y scaffolding para el proyecto Cordova, copiar el contenido del proyecto de inicio de la subcarpeta www.
Mover a la nueva carpeta de DirSearchClient.

    cd .\DirSearchClient

Agregar el complemento blanca, es necesario para llamar a la API de gráfico.

     cordova plugin add cordova-plugin-whitelist

A continuación, agregue todas las plataformas que desea admitir. Para disponer de una muestra de trabajo, debe ejecutar al menos uno de los comandos siguientes. Tenga en cuenta que no pueda emular iOS en Windows o Windows o Windows Phone en un MAC.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

Por último, puede agregar la ADAL para el complemento de Cordova a su proyecto.

    cordova plugin add cordova-plugin-ms-adal

## <a name="3-add-code-to-authenticate-users-and-obtain-tokens-from-aad"></a>*3. agregar código para autenticar a los usuarios y obtener tokens de AAD*

La aplicación que se desarrolla en este tutorial le proporcionará una característica de búsqueda de directorio descubierto, donde puede escribir el alias de cualquier usuario en el directorio y visualizar algunos atributos básicos del usuario final.  El proyecto de inicio contiene la definición de la interfaz de usuario básica de la aplicación (en www/index.html) y el soporte de temporal que cables de eventos de la aplicación básica ciclos de enlaces de la interfaz de usuario y lógica de presentación (en www/js/index.js) el resultado. Lo único hacia la izquierda para es agregar la lógica de implementación de tareas de identidad.

Lo primero que debe hacer es introducir en el código los valores de protocolo AAD utilizan para identificar la aplicación y los recursos de que destino. Estos valores se utilizará para construir las solicitudes de tokens más adelante. Insertar el fragmento de código a continuación en la parte superior del archivo index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

La `redirectUri` y `clientId` valores deben coincidir con la descripción de la aplicación en AAD. Puede encontrarlas de la ficha configurar en el portal de Azure, como se describe en el paso 1, más adelante en este tutorial.
Nota: si ha optado por no registrar una nueva aplicación en su propio inquilino, puede simplemente pegar los valores preconfigurados anteriores como sea - que le permitirá ver la ejecución de ejemplo, aunque siempre debe crear su propia entrada para las aplicaciones pensadas para producción.

A continuación, es necesario agregar el código de solicitud de token real. Insertar el fragmento de código siguiente entre el `search `y `renderdata `definiciones.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Examinemos esa función al dividir en dos partes principales.
En este ejemplo está diseñado para funcionar con cualquier inquilino, en lugar de para que esté vinculada a una determinada. Utiliza el extremo "/ comunes", lo que permite al usuario que escriba cualquier cuenta en tiempo de autenticación y dirige la solicitud al inquilino pertenece.
La primera parte del método inspecciona la caché de ADAL para ver si ya hay un símbolo almacenado - y si hay, usa a los inquilinos procede para volver a inicialización ADAL. Esto es necesario para evitar indicaciones adicionales, como el uso de "/ comunes" siempre da como resultado que le pregunta al usuario que escriba una nueva cuenta.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
La segunda parte del método realiza la solicitud tokewn correcta.
La `acquireTokenSilentAsync` método pide ADAL para devolver un símbolo para el recurso especificado sin mostrar cualquier UX. Esto puede ocurrir si la caché ya tiene un token de acceso adecuado almacenado, o si hay un símbolo de actualización que se pueden usar para obtener un nuevo token de acceso sin shwoing cualquier símbolo del sistema.
Si no lo consigue, nos retroceso `acquireTokenAsync` -visible que pide al usuario para autenticar.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Ahora que tenemos el token, por último podemos invocar la API de gráfico y realizar la consulta de búsqueda que queremos. Insertar el fragmento de código siguiente justo debajo de la `authenticate` definición.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Los archivos de punto de partida proporcionaron un equipos UX para introducir el alias de un usuario en un cuadro de texto. Este método utiliza ese valor para crear una consulta, combinar con el token de acceso, enviar al gráfico y analizar los resultados. El método renderData, que ya están en el archivo de punto de partida, se encarga para visualizar los resultados.

## <a name="4-run"></a>*4. ejecutar*
Es finalmente lista para ejecutar la aplicación. Operativo, es muy sencillo: una vez que se inicie la aplicación, escriba en el cuadro de texto el alias del usuario al que desea buscar -, a continuación, haga clic en el botón. Se le pedirá para la autenticación. Tras la autenticación correcta y búsqueda correcta, se mostrarán los atributos del usuario buscado. Ejecuciones subsiguientes realizará la búsqueda sin mostrar ningún indicador, gracias a la presencia en caché del token obtenido anteriormente.
Los pasos concretos para ejecutar la aplicación varían según la plataforma.

####<a name="windows-10"></a>Windows 10:

   Tablet PC:`cordova run windows --archs=x64 -- --appx=uap`

   Móvil (requiere el dispositivo móvil Windows10 conectado al PC):`cordova run windows --archs=arm -- --appx=uap --phone`

   __Nota__: durante la primera ejecución solicitará que inicie sesión en una licencia de desarrollador. Para obtener más detalles, vea [licencia de desarrollador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-81-tabletpc"></a>Tableta Windows 8.1 o PC:

   `cordova run windows`

   __Nota__: durante la primera ejecución solicitará que inicie sesión en una licencia de desarrollador. Para obtener más detalles, vea [licencia de desarrollador](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-phone-81"></a>Windows Phone 8.1:

   Para ejecutar en el dispositivo conectado:`cordova run windows --device -- --phone`

   Para ejecutar en emulador predeterminada:`cordova emulate windows -- --phone`

   Usar `cordova run windows --list -- --phone` para ver todos los destinos disponibles y `cordova run windows --target=<target_name> -- --phone` para ejecutar la aplicación en el dispositivo específico o emulador (por ejemplo, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####<a name="android"></a>Android:

   Para ejecutar en el dispositivo conectado:`cordova run android --device`

   Para ejecutar en emulador predeterminada:`cordova emulate android`

   __Nota__: asegúrese de que ha creado con el *Administrador de AVD* como que se muestra en la sección *requisitos previos* de instancia de emulador.

   Usar `cordova run android --list` para ver todos los destinos disponibles y `cordova run android --target=<target_name>` para ejecutar la aplicación en el dispositivo específico o emulador (por ejemplo, `cordova run android --target="Nexus4_emulator"`).

####<a name="ios"></a>iOS:

   Para ejecutar en el dispositivo conectado:`cordova run ios --device`

   Para ejecutar en emulador predeterminada:`cordova emulate ios`

   __Nota__: asegúrese de que tiene `ios-sim` paquete instalado para ejecutar en emulador. Para obtener más detalles, consulte la sección *requisitos previos* .

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Usar `cordova run --help` para ver la compilación adicionales y opciones de ejecución.

Para referencia, se proporciona el ejemplo completo (sin los valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).  Ahora puede mover a los escenarios más avanzados (Aceptar y más interesantes).  Desea probar:

[Proteger un Web Node.js API con Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
