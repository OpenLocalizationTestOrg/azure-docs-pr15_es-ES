<properties
    pageTitle="Introducción a Azure AD Android | Microsoft Azure"
    description="Cómo crear una aplicación Android que se integra con Azure AD para iniciar sesión y llamadas de Azure AD protegido API con OAuth."
    services="active-directory"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-android-app"></a>Integrar Azure AD en una aplicación de Android

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Si desarrolla una aplicación de escritorio, Azure AD facilita la forma muy sencilla para autenticar los usuarios con sus cuentas de Active Directory.  También permite la aplicación de forma segura consumir cualquier web API protegido por Azure AD, como las API de Office 365 o la API de Azure.

Para Android clientes que necesitan tener acceso a los recursos protegidos, Azure AD proporciona la Active Directory Authentication Library o ADAL.  Propósitos de ADAL en la vida es más fácil para su aplicación obtener el token de acceso.  Para mostrar solo lo fácil que es, a continuación, crearemos una aplicación de la lista de tareas pendientes Android:

-   Obtiene acceso a tokens para llamar a una API de lista de tareas pendientes mediante el [Protocolo de autenticación de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Obtiene la lista de tareas pendientes de un usuario
-   Usuarios de signos fuera.

Para empezar, necesitará a un inquilino de Azure AD en el que puede crear usuarios y registrar una aplicación.  Si aún no dispone de un inquilino, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).

> [AZURE.TIP] Pruebe la vista previa de nuestro nuevo [portal para desarrolladores](https://identity.microsoft.com/Docs/Android) que le ayudarán a ponerse en marcha con Azure Active Directory en tan solo unos minutos.  El portal de programadores lo guiará por el proceso de registrar una aplicación y la integración de Azure AD en el código.  Cuando haya terminado, tendrá una aplicación simple que puede autenticar a los usuarios en su inquilino y back-end que pueden aceptar tokens y realizar una validación. 

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>Paso 1: Descargar y ejecutar el servidor de ejemplo Node.js TODO de API de REST

Este ejemplo está escrito específicamente para que funcione con nuestro ejemplo existente para la creación de un sola inquilino API de REST de tareas pendientes para Microsoft Azure Active Directory. Este es un requisito previo para el inicio rápido.

Para obtener información sobre cómo configurar esto, visite nuestras muestras existentes aquí:

* [Servicio de API de REST de ejemplo de Microsoft Azure Active Directory para Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## <a name="step-2-register-your-web-api-with-your-microsoft-azure-ad-tenant"></a>Paso 2: Registrar su Web API con el inquilino de Microsoft Azure AD

**¿Qué estoy haciendo?**

*Microsoft Active Directory admite la adición de dos tipos de aplicaciones. Web API que ofrecen servicios para los usuarios y aplicaciones (ya sea en la web o una aplicación que se ejecute en un dispositivo) que tienen acceso a las API de Web. En este paso va a registrar la API de Web se ejecutan localmente para probar este ejemplo. Normalmente, esta API Web sería un servicio REST que ofrece funcionalidad que desee de una aplicación para obtener acceso a. ¡Microsoft Azure Active Directory puede proteger cualquier extremo!*

*Aquí se asume va a registrar la API de REST TODO mencionada anteriormente, pero esto funciona para cualquier API de Web que quiera Azure Active Directory para proteger.*

Pasos para registrar una API Web con Microsoft Azure AD

1. Inicie sesión en el [portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en Active Directory en la navegación de la izquierda
3. Haga clic en el inquilino de directorio donde desea registrar la aplicación de ejemplo.
4. Haga clic en la ficha aplicaciones.
5. En la caja, haga clic en Agregar.
6. Haga clic en "Agregar una aplicación de mi organización es desarrollar".
7. Escriba un nombre descriptivo para la Seleccionar aplicación, por ejemplo "TodoListService", "Web o aplicación Web API" y haga clic en siguiente.
8. Para la dirección URL de inicio de sesión, escriba la dirección URL base de la muestra, que es predeterminada `https://localhost:8080`.
9. Para el URI de identificador de aplicación, escriba `https://<your_tenant_name>/TodoListService`, reemplazar `<your_tenant_name>` con el nombre de su inquilino de Azure AD.  Haga clic en Aceptar para completar el registro.
10. Mientras se encuentra en el portal de Azure, haga clic en la ficha configurar de la aplicación.
11. **Buscar el valor del identificador de cliente y copiarlo tomarse un**, lo necesitará más adelante al configurar la aplicación.

## <a name="step-3-register-the-sample-android-native-client-application"></a>Paso 3: Registrar la aplicación de ejemplo Android Native Client

Registro de la aplicación web es el primer paso. A continuación, deberá informar a Azure Active Directory sobre la aplicación también. Esto permite a la aplicación para comunicarse con la API de Web solo registrados

**¿Qué estoy haciendo?**  

*Como se indicó anteriormente, Microsoft Azure Active Directory admite agregar dos tipos de aplicaciones. Web API que ofrecen servicios para los usuarios y aplicaciones (ya sea en la web o una aplicación que se ejecute en un dispositivo) que tienen acceso a las API de Web. En este paso va a registrar la aplicación en este ejemplo. Debe hacer esta aplicación puedan solicitud para acceder a la API de Web que acaba de registrar. Azure Active Directory rechaza incluso permitir la aplicación pedir sesión a menos que se registra! Forma parte de la seguridad del modelo.*

*Aquí se asume va a registrar esta aplicación de ejemplo mencionada, pero esto funciona para cualquier aplicación de que desarrollo.*

**¿Por qué estoy colocación una aplicación y una API de Web en un inquilino?**

*Como habrá adivinado, puede crear una aplicación que tiene acceso a una API externa que está registrada en Azure Active Directory desde otro inquilino. Si lo hace, los clientes le pedirá que consentimiento para el uso de la API en la aplicación. Es el elemento bueno, Active Directory Authentication Library para iOS se encarga de este consentimiento para usted! Como se tener acceso a características más avanzadas, verá que esto es una parte importante del trabajo necesario para tener acceso a la serie de Microsoft APIs de Azure y Office, así como cualquier otro proveedor de servicio. Por ahora, porque registró su Web API y la aplicación en el mismo inquilino, no verá las indicaciones consentimiento. Normalmente es el caso, si va a desarrollar una aplicación para su empresa usar.*

1. Inicie sesión en el [portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en Active Directory en la navegación de la izquierda
3. Haga clic en el inquilino de directorio donde desea registrar la aplicación de ejemplo.
4. Haga clic en la ficha aplicaciones.
5. En la caja, haga clic en Agregar.
6. Haga clic en "Agregar una aplicación de mi organización es desarrollar".
7. Escriba un nombre descriptivo para la aplicación, por ejemplo "TodoListClient-Android", seleccione "cliente aplicación nativa" y haga clic en siguiente.
8. Para la redirección URI, escriba `http://TodoListClient`.  Haga clic en Finalizar.
9. Haga clic en la ficha configurar de la aplicación.
10. Buscar el valor del identificador de cliente y copiarlo aparte, lo necesitará más adelante al configurar la aplicación.
11. En "Permisos a otras aplicaciones,", haga clic en "Agregar aplicación".  Seleccione "Sí" en el menú desplegable "Mostrar" y haga clic en la marca de verificación superior.  Busque y haga clic en el TodoListService y haga clic en la marca de verificación de la parte inferior para agregar la aplicación.  Seleccione "Acceso TodoListService" en la lista desplegable "Delegar permisos" y guardar la configuración.



Para compilar con Maven, puede usar el pom.xml en el nivel superior

  * Clonar este repo en un directorio de su elección:

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`  

  * Siga los pasos indicados en la [sección Prerequests configurar su experto para android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)
  * Configurar emulador con SDK 19
  * Vaya a la carpeta raíz donde se clona la repo
  * Ejecute el comando: instalar mvn limpiar
  * Cambie el directorio a la muestra de inicio rápido: cd samples\hello
  * Ejecute el comando: mvn android: implementar android: ejecutar
  * Debería ver Iniciar aplicación
  * ¡Escriba las credenciales de usuario de prueba para probar!

Paquetes de jar se enviará también al lado del paquete aar.

### <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>Paso 4: Descargar ADAL Android y agregar al área de trabajo Eclipse

Lo hemos hecho fácil tiene varias opciones para usar esta biblioteca en su proyecto Android:

* Puede usar el código fuente para importar esta biblioteca en Eclipse y vincularlo a la aplicación.
* Si usa Android Studio, puede usar el formato de paquete *aar* y hacen referencia a los archivos binarios.

####<a name="option-1-source-zip"></a>Opción 1: Origen Zip

Para descargar una copia del código fuente, haga clic en "Descargar" ZIP"en el lado derecho de la página o haga clic en [aquí](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz).

####<a name="option-2-source-via-git"></a>Opción 2: Origen mediante Git

Para obtener el código fuente del SDK mediante git simplemente escriba:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####<a name="option-3-binaries-via-gradle"></a>Opción 3: Binarios a través de Gradle

Puede obtener los archivos binarios de repo central de experto. Paquete AAR pueden incluir los siguientes en el proyecto en AndroidStudio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####<a name="option-4-aar-via-maven"></a>Opción 4: aar a través de experto

Si está utilizando el complemento m2e en Eclipse, puede especificar la dependencia en el archivo pom.xml:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####<a name="option-5-jar-package-inside-libs-folder"></a>Opción 5: jar paquete dentro de la carpeta de bibliotecas
Puede obtener el archivo jar de experto la repo y suéltelos en la carpeta de *bibliotecas* en su proyecto. Debe copiar los recursos necesarios a su proyecto, así como los paquetes de jar no incluyen.


### <a name="step-5-add-references-to-android-adal-to-your-project"></a>Paso 5: Agregar referencias a Android ADAL a su proyecto


2. Agregar una referencia a su proyecto y especifique como una biblioteca de Android. Si no está seguro de cómo hacerlo, [haga clic aquí para obtener más información] (http://developer.android.com/tools/projects/projects-eclipse.html)

3. Agregar la dependencia de proyecto para la depuración en la configuración del proyecto

4. Actualizar el archivo del proyecto AndroidManifest.xml para incluir:

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. Crear una instancia de AuthenticationContext en su actividad principal. Los detalles de esta llamada están fuera del ámbito de este archivo Léame, pero puede empezar a trabajar consultando la [Android ejemplo nativo de cliente](https://github.com/AzureADSamples/NativeClient-Android). A continuación se muestra un ejemplo:

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * Nota: mContext es un campo de su actividad

8. Copie este bloque de código para controlar el final de AuthenticationActivity después de que escribe las credenciales de usuario y recibe el código de autorización:

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. Para solicitar un token, defina una devolución de llamada

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. Por último, pida a un token con esa devolución de llamada:

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

Explicación de los parámetros:

  * Recurso es necesario y es el recurso que intenta obtener acceso.
  * ClientID es necesario y viene desde el AzureAD Portal.
  * Puede configurar redirectUri como su packagename. No es necesario que se debe proporcionar para la llamada de acquireToken.
  * PromptBehavior ayuda a pedir credenciales omitir la memoria caché y cookies.
  * Devolución de llamada se llama cuando se cambia el código de autorización por un símbolo.

  La devolución de llamada tendrá un objeto de AuthenticationResult que tiene accesstoken, fecha expirado e información de idtoken.

Opcional: **acquireTokenSilent**

Puede llamar a **acquireTokenSilent** para controlar el almacenamiento en caché y actualización de token. Proporciona también la versión de sincronización. Identificador de usuario acepta como parámetro.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Agente**: aplicación del portal de empresa de Intune Microsoft le proporcionará el componente de agente. ADAL usar la cuenta de agente, si hay una cuenta de usuario se crea en este autenticador y programador decide no omitirlo. Programador puede omitir el usuario intermediario con:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 Programador debe registrar redirectUri especial para uso de agente. RedirectUri se encuentra en el formato de msauth://packagename/Base64UrlencodedSignature. Puede obtener la redirecturi para la aplicación con la secuencia de comandos "brokerRedirectPrint.ps1" o usar API llamada mContext.getBrokerRedirectUri. Firma relacionada con los certificados de firma.

 Modelo de agente actual es de un usuario. AuthenticationContext proporciona el método de la API para obtener el agente de usuario.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
 ```
 Usuario de agente se devolverá si la cuenta es válida.

 El manifiesto de aplicación debe tener permisos para usar cuentas de administrador de cuentas: http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


En este tutorial debe tener necesita integrar correctamente con Azure Active Directory. Para obtener más ejemplos de esto funcione, visite la AzureADSamples / repositorio de GitHub.

## <a name="important-information"></a>Información importante

### <a name="customization"></a>Personalización

Recursos de proyecto de la biblioteca se pueden sobrescribir por los recursos de la aplicación. Esto ocurre cuando la creación de la aplicación. Por este motivo, puede personalizar el diseño de actividad de autenticación como desee. Debe asegurarse de que debe tener el identificador de los controles que uses(Webview) ADAL.

### <a name="broker"></a>Agente

Componente agente se entregarán con la aplicación del portal de empresa de Intune Microsoft. Cuenta se creará en el Administrador de cuenta. Tipo de cuenta es "com.microsoft.workaccount". Solo permite cuenta SSO única. Después de completar el desafío de dispositivo para una de las aplicaciones creará cookie SSO para este usuario.

### <a name="authority-url-and-adfs"></a>ADFS y la dirección Url de la entidad

ADFS no se reconoce como producción STS, por lo que debe activar de detección de la instancia y pasar false al constructor AuthenticationContext.

Dirección url de la entidad emisora debe STS instancia y el nombre del inquilino: https://login.windows.net/yourtenant.onmicrosoft.com

### <a name="querying-cache-items"></a>Consultar los elementos de la caché

ADAL proporciona funciones de consulta de caché de forma predeterminada en SharedPreferences con algunas caché simple. Puede obtener la memoria caché actual de AuthenticationContext con:
```Java
 ITokenCacheStore cache = mContext.getCache();
```
También puede proporcionar la implementación de la memoria caché, si desea personalizar.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### <a name="promptbehavior"></a>PromptBehavior

ADAL proporciona la opción para especificar el comportamiento del mensaje. Interfaz de usuario aparecerá PromptBehavior.Auto si token de actualización no es válido y se necesitan credenciales de usuario. PromptBehavior.Always omitirá el uso de la memoria caché y mostrar siempre la interfaz de usuario.

### <a name="silent-token-request-from-cache-and-refresh"></a>Solicitud de token silencioso de caché y actualización

Este método no utiliza pop IU arriba y no requiere una actividad. Devolverá token de caché si está disponible. Si ha caducado token, intentará actualizarla. Si el token de actualización se ha expirado o no se pudo, devolverá AuthenticationException.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

También puede hacer sincronización llamar con este método. Puede establecer en null a devolución de llamada o usar acquireTokenSilentSync.

### <a name="diagnostics"></a>Diagnósticos

Las siguientes son las fuentes principales de la información para diagnosticar problemas:

+ Excepciones
+ Registros
+ Trazas de red

Además, observe que los identificadores de correlación central de los diagnósticos de la biblioteca. Puede establecer su correlación solicitar identificadores de forma de solicitud por si desea relacionar un ADAL con otras operaciones en el código. Si no establece un identificador de correlación ADAL generará un aleatorio uno y todos los mensajes de registro y llamadas de red estarán marcadas con el identificador de correlación. El automática genera cambios id en cada solicitud.

#### <a name="exceptions"></a>Excepciones

Evidentemente, esto es el primer diagnóstico. Tratamos de proporcionar mensajes de error útiles. Si encuentra una que no es útil y archivos, un problema, háganoslo saber. Proporcione también información del dispositivo como modelo y SDK #.

#### <a name="logs"></a>Registros

Puede configurar la biblioteca para generar mensajes de registro que puede usar para diagnosticar problemas. Configurar el registro que realiza la llamada siguiente para configurar una devolución de llamada ADAL utilizará a mano desactivar cada mensaje en el registro que se genera.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
 ```
Los mensajes se pueden escribir en un archivo de registro personalizado tal como se muestra a continuación. Desgraciadamente, no hay ninguna manera estándar de obtener registros en un dispositivo. Hay algunos servicios que pueden ayudarle con este. Puede también de inventario personalizada, como enviar el archivo a un servidor.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### <a name="logging-levels"></a>Niveles de registro

+ Error(Exceptions)
+ Warn(Warning)
+ Información (informativo)
+ Detallado (más detalles)

Establecer el nivel de registro similar a esta:
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 Todos los mensajes de registro se envían a logcat además de cualquier devoluciones de llamada de registro personalizado.
Puede obtener registro a una logcat de formulario de archivo como se muestra belog:

 ```
  adb logcat > "C:\logmsg\logfile.txt"
 ```
 Más ejemplos sobre comandos adb: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### <a name="network-traces"></a>Trazas de red

Puede usar varias herramientas para capturar el tráfico HTTP genera ADAL.  Esto es muy útil si está familiarizado con el protocolo OAuth o si debe proporcionar información de diagnóstico a Microsoft o de otros canales de soporte técnico.

Fiddler es la herramienta de seguimiento de HTTP más sencilla.  Use los siguientes vínculos configurar hasta el tráfico de red ADAL registro correctamente.  Para ser útil es necesario configurar fiddler o cualquier otra herramienta como Charles grabar sin cifrar tráfico SSL.  Nota: Los seguimientos de esta manera pueden contener información amplios privilegios como símbolos de acceso, nombres de usuario y contraseñas.  Si está utilizando las cuentas de producción, no compartir estas trazas con partes 3ª.  Si necesita proporcionar un seguimiento a alguien para obtener soporte técnico, provocan el problema con una cuenta temporal con nombres de usuario y contraseñas que no le importa uso compartido.

+ [Configurar Fiddler para Android](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [Configurar reglas de Fiddler para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### <a name="dialog-mode"></a>Modo de cuadro de diálogo
método acquireToken sin actividad es compatible con el símbolo del sistema del cuadro de diálogo.

### <a name="encryption"></a>Cifrado

ADAL cifra los tokens y la tienda en SharedPreferences de forma predeterminada. Puede buscar en la clase StorageHelper para ver los detalles. Android presentó AndroidKeyStore para 4.3(API18) de almacenamiento seguro de claves privadas. ADAL usa para API18 y más. Si desea usar ADAL para versiones SDK inferiores, debe proporcionar una clave secreta en AuthenticationSettings.INSTANCE.setSecretKey

### <a name="oauth2-bearer-challenge"></a>Desafío de portador Oauth2

Clase AuthenticationParameters proporciona la funcionalidad para obtener la authorization_uri de desafío de portador Oauth2.

### <a name="session-cookies-in-webview"></a>Cookies de sesión en la vista Web

Vista Web Android desactive las cookies de sesión después de cerrar la aplicación. Se puede controlar con código de ejemplo siguiente:
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
Más información acerca de las cookies: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### <a name="resource-overrides"></a>Reemplaza de recursos

La biblioteca ADAL incluye cadenas de inglés para los siguientes mensajes ProgressDialog.

La aplicación debería sobrescribirlos si desean cadenas localizadas.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### <a name="ntlm-dialog"></a>Cuadro de diálogo NTLM
Adal versión 1.1.0 es compatible con el cuadro de diálogo NTLM que se procesa a través de evento onReceivedHttpAuthRequest desde WebViewClient. Pueden personalizar cadenas y diseño de cuadro de diálogo.

### <a name="cross-app-sso"></a>SSO entre aplicaciones
Aprenda [a habilitar SSO entre aplicaciones en Android con ADAL](active-directory-sso-android.md)  


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
