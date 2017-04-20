<properties
    pageTitle="Azure Active B2C de directorio: Llame un web API desde una aplicación Android | Microsoft Azure"
    description="En este artículo le mostrará cómo crear un dispositivo Android 'lista de tareas pendientes' aplicación que llama a un sitio web de Node.js API utilizando tokens portador de OAuth 2.0. La aplicación Android y la web API usar B2C de Azure Active Directory para administrar las identidades de usuario y autenticar a los usuarios."
    services="active-directory-b2c"
    documentationCenter="android"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>B2C de Azure AD: Llamar a un sitio web API desde una aplicación de Android

> [AZURE.WARNING] Este tutorial requiere algunas actualizaciones importantes, específicamente para eliminar el uso de ADAL Android para B2C.  Vamos a publicar un nuevo instrucciones para el uso de Azure AD B2C en aplicaciones para Android en la próxima semana y, a continuación, le recomendamos que mantenga hasta ese momento.  Pero si desea realizar pruebas, no dude en continuar con el siguiente artículo.



Usando B2C de Azure Active Directory (AD Azure), puede agregar las características de administración de identidad de autoservicio eficaces a las aplicaciones para Android y web API en pocos pasos. En este artículo le mostrará cómo crear un dispositivo Android "lista de tareas pendientes" aplicación que llama a un sitio web de Node.js API utilizando tokens portador de OAuth 2.0. La aplicación Android y la web API use Azure AD B2C para administrar identidades de usuario y la autenticación de usuarios.

En este tutorial debe tener un web API protegido por Azure AD con B2C trabajar totalmente. Hemos creado uno para .NET y Node.js para su uso. Este tutorial se supone que la muestra de web API Node.js está configurada. Para obtener más información, vea la [Azure AD B2C web API Node.js tutorial](active-directory-b2c-devquickstarts-api-node.md).

Para obtener información sobre los clientes Android que necesitan tener acceso a los recursos protegidos, Azure AD proporciona la biblioteca de autenticación de Active Directory (ADAL). Propósitos de ADAL es más fácil para su aplicación obtener el token de acceso. Para demostrar lo fácil que es, en esta guía vamos a crear una aplicación de la lista de tareas pendientes Android que:

- Obtiene el token de acceso que llaman a una lista de tareas pendientes API mediante el [Protocolo de autenticación de OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
- Obtiene las listas de tareas pendientes de los usuarios.
- Signos de los usuarios.

> [AZURE.NOTE] Este artículo no trata sobre cómo implementar el inicio de sesión, suscripción y administración de perfiles mediante el uso de Azure AD B2C. Se centra en cómo llamar a las API de web después de que el usuario se autentica. Si todavía no lo ha hecho, debe empezar con la [.NET web app tutorial de introducción](active-directory-b2c-devquickstarts-web-dotnet.md) obtener información sobre los conceptos básicos de Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C

Antes de poder usar Azure AD B2C, debe crear un directorio o de inquilinos. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos y más. Si no tiene uno ya, [Cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar en esta guía.

## <a name="create-an-application"></a>Crear una aplicación

A continuación, deberá crear una aplicación en el directorio B2C. Esto le da información de Azure AD que necesita para comunicarse de forma segura con la aplicación. La aplicación y la web API están representados por un solo **Identificador de la aplicación** en este caso, debido a que incluyen una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web**/**web API** en la aplicación.
- Escriba `urn:ietf:wg:oauth:2.0:oob` como una **dirección URL de respuesta**. Es la dirección URL predeterminada para este código de ejemplo.
- Crear un **secreto de aplicación** de la aplicación y copiarlo. Lo necesitará más adelante. Tenga en cuenta que este valor debe ser [un carácter de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de usarla.
- Copie el **Identificador de la aplicación** que se asigna a la aplicación. También necesitará esto más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

En Azure AD B2C, cada experiencia del usuario se define por una [Directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene tres experiencias de identidad: inicio de sesión, inicio de sesión e inicie sesión mediante el uso de Facebook.  Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de la directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando crea su tres directivas, asegúrese de:

- Elija el **nombre para mostrar** y otros atributos de la suscripción en la directiva de suscripción.
- Elija el **nombre para mostrar** y el **Identificador de objeto de** notificaciones de la aplicación en cada directiva. Puede elegir otras reclamaciones también.
- Copiar el **nombre** de cada directiva después de crearlo. Debe tener el prefijo `b2c_1_`.  Necesitará estos nombres directivas más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las tres directivas, está listo para crear la aplicación.

Tenga en cuenta que este artículo no trata sobre cómo usar las directivas que acaba de crear. Para obtener información sobre cómo funcionan las directivas de Azure AD B2C, comience con la [.NET web app tutorial de introducción](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Descargar el código

El código de este tutorial [se mantiene según GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android). Para generar el ejemplo mientras avanza, puede [descargar el proyecto de esquema como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/skeleton.zip). También puede clonar la estructura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-Android.git
```

> [AZURE.NOTE] **Se le pide que descargue el esquema para completar este tutorial.** Debido a la complejidad de la implementación de una aplicación de Android totalmente funcional, la estructura tiene código UX que se ejecutará después de completar este tutorial. Esta es una medida de ahorro de tiempo para los desarrolladores. El código UX no es germane el tema de cómo agregar B2C a una aplicación de Android.

La aplicación completada también está [disponible como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip) o en la `complete` rama del mismo repositorio.

Para compilar con Maven, puede usar `pom.xml` en el nivel superior.

  1. Siga los pasos de [la sección Requisitos previos para configurar experto para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android).
  2. Configurar un emulador con SDK 21.
  3. Vaya a la carpeta raíz donde se clona la repo.
  4. Ejecute el comando `mvn clean install`.
  5. Cambiar el directorio del ejemplo de tutorial rápido `cd samples\hello`.
  6. Ejecute el comando `mvn android:deploy android:run`.

Verá la aplicación de inicio. Escriba las credenciales de usuario de prueba para probar la aplicación.

Paquetes de Java Archive (JAR) se enviará también al lado del paquete de archivar Android (AAR).

## <a name="download-the-android-adal-and-add-it-to-your-android-studio-workspace"></a>Descargar ADAL Android y agregar al área de trabajo Studio Android

Tiene opciones para cómo usar esta biblioteca en su proyecto Android:

* Puede usar el código fuente para importar la biblioteca en Eclipse y vincularlo a la aplicación.
* Si usa Android Studio, puede usar el formato de paquete AAR y hacer referencia a los archivos binarios.

### <a name="option-1-binaries-via-gradle-recommended"></a>Opción 1: Binarios a través de Gradle (recomendado)

Puede obtener los archivos binarios de la repo central de experto. El paquete AAR pueden incluir en el proyecto en Android Studio (por ejemplo, en `build.gradle`) de este modo:

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
    compile('com.microsoft.aad:adal:2.0.1-alpha') {
        exclude group: 'com.android.support'
    } // Recent version is 2.0.1-alpha
}
```

### <a name="option-2-aar-via-maven"></a>Opción 2: AAR a través de experto

Si utiliza la `m2e` complemento en Eclipse, puede especificar la dependencia en su `pom.xml` archivo:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>2.0.1-alpha</version>
    <type>aar</type>
</dependency>
```

### <a name="option-3-source-via-git-last-resort"></a>Opción 3: De origen a través de Git (último recurso)

Para obtener el código fuente del SDK mediante Git, escriba:

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

Utilice la rama **convergencia.**

## <a name="set-up-your-configuration-file"></a>Configurar el archivo de configuración

Usar la configuración que haya configurado anteriormente en el portal de B2C para configurar el proyecto Android.

Abrir `helpes/Constants.java` y rellene los valores de las siguientes acciones:

```

package com.microsoft.aad.taskapplication.helpers;

import com.microsoft.aad.adal.AuthenticationResult;

public class Constants {

    public static final String SDK_VERSION = "1.0";
    public static final String UTF8_ENCODING = "UTF-8";
    public static final String HEADER_AUTHORIZATION = "Authorization";
    public static final String HEADER_AUTHORIZATION_VALUE_PREFIX = "Bearer ";

    // -------------------------------AAD
    // PARAMETERS----------------------------------
    public static String AUTHORITY_URL = "https://login.microsoftonline.com/hypercubeb2c.onmicrosoft.com/";
    public static String CLIENT_ID = "<your application id>";
    public static String[] SCOPES = {"<your application id>"};
    public static String[] ADDITIONAL_SCOPES = {""};
    public static String REDIRECT_URL = "<redirect uri>";
    public static String CORRELATION_ID = "";
    public static String USER_HINT = "";
    public static String EXTRA_QP = "";
    public static String FB_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNIN_POLICY = "B2C_1_<your policy>";
    public static String EMAIL_SIGNUP_POLICY = "B2C_1_<your policy>";
    public static boolean FULL_SCREEN = true;
    public static AuthenticationResult CURRENT_RESULT = null;
    // Endpoint we are targeting for the deployed WebAPI service
    public static String SERVICE_URL = "http://localhost:3000/tasks";

    // ------------------------------------------------------------------------------------------

    static final String TABLE_WORKITEM = "WorkItem";
    public static final String SHARED_PREFERENCE_NAME = "com.example.com.test.settings";


}


```
- `SCOPES`: Los ámbitos que se pasan al servidor que desea solicitar del servidor cuando un usuario inicia sesión. Para la vista previa B2C, pasa `client_id`. Sin embargo, se espera para cambiar a `read scopes` en el futuro. Cuando esto sucede, se actualizará este documento.
- `ADDITIONAL_SCOPES`: Ámbitos adicionales que desee usar para la aplicación. Que se esperan que se utilizarán en el futuro.
- `CLIENT_ID`: El identificador de aplicación obtuvo desde el portal.
- `REDIRECT_URL`: La redirección donde espera el token de se envíe de vuelta.
- `EXTRA_QP`: Nada adicional que desea pasar al servidor en un formato de URL codificada.
- `FB_POLICY`: La directiva que se invoca. Esta es la parte más importante de este tutorial.
- `EMAIL_SIGNIN_POLICY`: La directiva que se invoca. Esta es la parte más importante de este tutorial.
- `EMAIL_SIGNUP_POLICY`: La directiva que se invoca. Esta es la parte más importante de este tutorial.

## <a name="add-references-to-android-adal-to-your-project"></a>Agregar referencias a Android ADAL a su proyecto

> [AZURE.NOTE]  ADAL para Android utiliza un modelo de objetivo para invocar autenticación. Color "Diseñar" la aplicación para que funcione. En este ejemplo completo y todos los ADAL para Android, centros sobre cómo administrar tipos de color y pasar información entre ellas.

En primer lugar, informar a Android sobre el diseño de la aplicación, incluidos los tipos de color que desee usar. Estos tipos de color se explica detalladamente más adelante en este tutorial.

Actualizar su proyecto `AndroidManifest.xml` archivo para incluir todos los tipos de color:

```
   <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.microsoft.aad.taskapplication"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="11"
        android:targetSdkVersion="19" />

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.microsoft.aad.adal.AuthenticationActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:exported="true"
            android:label="@string/title_login_hello_app" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.LoginActivity"
            android:configChanges="orientation|keyboardHidden|screenSize"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.UsersListActivity"
            android:label="@string/title_activity_feed" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.SettingsActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.AddTaskActivity"
            android:label="@string/title_activity_settings" >
        </activity>
        <activity
            android:name="com.microsoft.aad.taskapplication.ToDoActivity"
            android:label="@string/app_name" >
        </activity>
    </application>

</manifest>    
```

Como puede ver, se definen cinco actividades. Use todas estas.

- `AuthenticationActivity`: Esto proviene de ADAL y proporciona la vista web de inicio de sesión.
- `LoginActivity`: Muestra las directivas de inicio de sesión y los botones para cada directiva.
- `SettingsActivity`: Use esta opción para cambiar la configuración de la aplicación en tiempo de ejecución.
- `AddTaskActivity`: Use esta opción para agregar tareas a la API de REST que están protegidos por Azure AD.
- `ToDoActivity`: Esta es la actividad principal que muestra las tareas.

## <a name="create-the-sign-in-activity"></a>Crear la actividad de inicio de sesión

Crear una actividad principal y llámelo `LoginActivity`.

Cree un archivo llamado `LoginActivity.java`.

Debe inicializar la actividad y agregue algunos botones que va a controlar la interfaz de usuario. Esto es familiar si ha escrito código Android antes.

```
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;

/**
 * Created by brwerner on 9/17/15.
 */
public class LoginActivity extends Activity {

    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_signin);
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        Button button = (Button) findViewById(R.id.signin_facebook);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.FB_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signin_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNIN_POLICY);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.signup_email);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(LoginActivity.this, ToDoActivity.class);
                intent.putExtra("thePolicy", Constants.EMAIL_SIGNUP_POLICY);
                startActivity(intent);

            }
        });

    }

}


```
Acaba de crear botones que llame a su `ToDoActivity` objetivo (que llama ADAL cuando necesite un token). Hacer esto mediante su actividad como una referencia y un parámetro adicional. Este parámetro adicional se pasa por el `intent.putExtra()` método. Defina `"thePolicy"` usando especificada en `Constants.java`. Esto indica la finalidad de la directiva que invocar durante la autenticación.

## <a name="create-the-settings-activity"></a>Crear la actividad de configuración

Se trata de una actividad que rellena la configuración de la interfaz de usuario.

Cree un archivo llamado `SettingsActivity.java` para simples crear, leer, actualizar y eliminar operaciones (CRUD).

```
 package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.content.SharedPreferences;
import android.content.SharedPreferences.Editor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.Switch;
import android.widget.TextView;

import com.microsoft.aad.taskapplication.helpers.Constants;

/**
 * Settings page to try broker by options
 */
public class SettingsActivity extends Activity {

    //private CheckBox checkboxAskBroker, checkboxCheckBroker;
    private Switch fullScreenSwitch;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_settings);

        loadSettings();
//      checkboxAskBroker = (CheckBox) findViewById(R.id.askInstall);
//      checkboxCheckBroker = (CheckBox) findViewById(R.id.useBroker);

        Button save = (Button) findViewById(R.id.settingsSave);

        save.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                TextView textView = (TextView)findViewById(R.id.authority);
                Constants.AUTHORITY_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.resource);
            //    Constants.SCOPES = textView.getText().toString();

                textView = (TextView)findViewById(R.id.clientId);
                Constants.CLIENT_ID = textView.getText().toString();

                textView = (TextView)findViewById(R.id.extraQueryParameters);
                Constants.EXTRA_QP = textView.getText().toString();

                textView = (TextView)findViewById(R.id.redirectUri);
                Constants.REDIRECT_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                Constants.SERVICE_URL = textView.getText().toString();

                textView = (TextView)findViewById(R.id.serviceUrl);
                textView.setText(Constants.SERVICE_URL);

                textView = (TextView)findViewById(R.id.fb_signin);
                textView.setText(Constants.FB_POLICY);

                textView = (TextView)findViewById(R.id.email_signin);
                textView.setText(Constants.EMAIL_SIGNIN_POLICY);

                textView = (TextView)findViewById(R.id.email_signup);
                textView.setText(Constants.EMAIL_SIGNUP_POLICY);

                textView = (TextView)findViewById(R.id.correlationId);
                textView.setText(Constants.CORRELATION_ID);

                fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
                Constants.FULL_SCREEN = fullScreenSwitch.isChecked();

                finish();
            }
        });


    }

    private void loadSettings() {
        TextView textView = (TextView)findViewById(R.id.authority);
        textView.setText(Constants.AUTHORITY_URL);
        textView = (TextView)findViewById(R.id.resource);
        textView.setText(Constants.SCOPES[0]);
        textView = (TextView)findViewById(R.id.clientId);
        textView.setText(Constants.CLIENT_ID);
        textView = (TextView)findViewById(R.id.extraQueryParameters);
        textView.setText(Constants.EXTRA_QP);
        textView = (TextView)findViewById(R.id.redirectUri);
        textView.setText(Constants.REDIRECT_URL);
        textView = (TextView)findViewById(R.id.serviceUrl);
        textView.setText(Constants.SERVICE_URL);
        textView = (TextView)findViewById(R.id.fb_signin);
        textView.setText(Constants.FB_POLICY);
        textView = (TextView)findViewById(R.id.email_signin);
        textView.setText(Constants.EMAIL_SIGNIN_POLICY);
        textView = (TextView)findViewById(R.id.email_signup);
        textView.setText(Constants.EMAIL_SIGNUP_POLICY);
        textView = (TextView)findViewById(R.id.correlationId);
        textView.setText(Constants.CORRELATION_ID);

        fullScreenSwitch = (Switch)findViewById(R.id.fullScreen);
        fullScreenSwitch.setChecked(Constants.FULL_SCREEN);
    }

    private void saveSettings(String key, boolean value) {
        SharedPreferences prefs = SettingsActivity.this.getSharedPreferences(
                Constants.SHARED_PREFERENCE_NAME, Activity.MODE_PRIVATE);
        Editor prefsEditor = prefs.edit();
        prefsEditor.putBoolean(key, value);
        prefsEditor.commit();
    }
}
```

## <a name="create-the-add-task-activity"></a>Crear la actividad de tarea de agregar

Puede usar esta actividad para agregar una tarea a su punto final de la API de REST.

Cree un archivo llamado `AddTaskActivity.java` y escriba lo siguiente.

```
package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;

public class AddTaskActivity extends Activity {

    EditText textField;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_add_task);
        textField = (EditText) findViewById(R.id.taskToAdd);
        Button button = (Button) findViewById(R.id.postTaskbutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (textField.getText().toString() != null
                        && !textField.getText().toString().trim().isEmpty()
                        && Constants.CURRENT_RESULT != null) {

                    TodoListHttpService service = new TodoListHttpService();
                    try {
                        service.addItem(textField.getText().toString(), Constants.CURRENT_RESULT.getAccessToken());
                    } catch (Exception e) {
                        SimpleAlertDialog.showAlertDialog(getApplicationContext(), "Exception caught", e.getMessage());
                    }
                    finish();
                }
            }
        });
    }

}

```

## <a name="create-the-to-do-list-activity"></a>Crear la actividad de la lista de tareas pendientes

Esta es la actividad más importante. Puede utilizarla para obtener un token de Azure AD para una directiva y, a continuación, utilizar ese token para llamar al servidor de la API de REST de tarea.

Cree un archivo llamado `ToDoActivity.java` y escriba lo siguiente. (Las llamadas se explicará más adelante.)

```

package com.microsoft.aad.taskapplication;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.ProgressDialog;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.webkit.CookieManager;
import android.webkit.CookieSyncManager;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import com.microsoft.aad.adal.AuthenticationCallback;
import com.microsoft.aad.adal.AuthenticationContext;
import com.microsoft.aad.adal.AuthenticationResult;
import com.microsoft.aad.adal.AuthenticationSettings;
import com.microsoft.aad.adal.UserIdentifier;
import com.microsoft.aad.adal.PromptBehavior;
import com.microsoft.aad.taskapplication.helpers.Constants;
import com.microsoft.aad.taskapplication.helpers.InMemoryCacheStore;
import com.microsoft.aad.taskapplication.helpers.TodoListHttpService;
import com.microsoft.aad.taskapplication.helpers.Utils;
import com.microsoft.aad.taskapplication.helpers.WorkItemAdapter;


import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URL;
import java.security.NoSuchAlgorithmException;
import java.security.spec.InvalidKeySpecException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ToDoActivity extends Activity {



    private final static String TAG = "ToDoActivity";

    private AuthenticationContext mAuthContext;
    private static AuthenticationResult sResult;

    /**
     * Adapter to sync the items list with the view
     */
    private WorkItemAdapter mAdapter = null;

    /**
     * Show this dialog when activity first launches to check if user has login
     * or not.
     */
    private ProgressDialog mLoginProgressDialog;


    /**
     * Initializes the activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_list_todo_items);
        CookieSyncManager.createInstance(getApplicationContext());
        Toast.makeText(getApplicationContext(), TAG + "LifeCycle: OnCreate", Toast.LENGTH_SHORT)
                .show();

        // Clear previous sessions
        clearSessionCookie();
        try {
            // Provide key info for Encryption
            if (Build.VERSION.SDK_INT < 18) {
                Utils.setupKeyForSample();
            }

        Button button = (Button) findViewById(R.id.addTaskButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, AddTaskActivity.class);
                startActivity(intent);
            }
        });

        button = (Button) findViewById(R.id.appSettingsButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, SettingsActivity.class);
                startActivity(intent);

            }
        });

        button = (Button) findViewById(R.id.switchUserButton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(ToDoActivity.this, LoginActivity.class);
                startActivity(intent);

            }
        });


        final TextView name = (TextView)findViewById(R.id.userLoggedIn);


        mLoginProgressDialog = new ProgressDialog(this);
        mLoginProgressDialog.requestWindowFeature(Window.FEATURE_NO_TITLE);
        mLoginProgressDialog.setMessage("Login in progress...");
        mLoginProgressDialog.show();
        // Ask for token and provide callback
        try {
            mAuthContext = new AuthenticationContext(ToDoActivity.this, Constants.AUTHORITY_URL,
                    false);
            String policy = getIntent().getStringExtra("thePolicy");

            if(Constants.CORRELATION_ID != null &&
                    Constants.CORRELATION_ID.trim().length() !=0){
                mAuthContext.setRequestCorrelationId(UUID.fromString(Constants.CORRELATION_ID));
            }

            AuthenticationSettings.INSTANCE.setSkipBroker(true);

            mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES, policy, Constants.CLIENT_ID,
                    Constants.REDIRECT_URL, getUserInfo(), PromptBehavior.Always,
                    "nux=1&" + Constants.EXTRA_QP,
                    new AuthenticationCallback<AuthenticationResult>() {

                        @Override
                        public void onError(Exception exc) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }
                            SimpleAlertDialog.showAlertDialog(ToDoActivity.this,
                                    "Failed to get token", exc.getMessage());
                        }

                        @Override
                        public void onSuccess(AuthenticationResult result) {
                            if (mLoginProgressDialog.isShowing()) {
                                mLoginProgressDialog.dismiss();
                            }

                            if (result != null && !result.getToken().isEmpty()) {
                                setLocalToken(result);
                                updateLoggedInUser();
                                getTasks();
                                ToDoActivity.sResult = result;
                                Toast.makeText(getApplicationContext(), "Token is returned", Toast.LENGTH_SHORT)
                                        .show();

                                if (sResult.getUserInfo() != null) {
                                    name.setText(result.getUserInfo().getDisplayableId());
                                    Toast.makeText(getApplicationContext(),
                                            "User:" + sResult.getUserInfo().getDisplayableId(), Toast.LENGTH_SHORT)
                                            .show();
                                }
                            } else {
                                //TODO: popup error alert
                            }
                        }
                    });
        } catch (Exception e) {
            SimpleAlertDialog.showAlertDialog(ToDoActivity.this, "Exception caught", e.getMessage());
        }
        Toast.makeText(ToDoActivity.this, TAG + "done", Toast.LENGTH_SHORT).show();
    } catch (InvalidKeySpecException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }}

```


 Habrá observado que esta se basa en métodos que aún no ha escrito todavía. Incluyen `updateLoggedInUser()`, `clearSessionCookie()`, y `getTasks()`. Deberá escribirlos más adelante en esta guía. Puede ignorar los errores en Android Studio por ahora.

Explicación de los parámetros:

  - `SCOPES`: Requerido, los ámbitos que intenta solicita el acceso. Para la vista previa B2C, esto es el mismo que `client_id`, pero se espera que cambie en el futuro.
  - `POLICY`: La directiva cuando desee autenticar al usuario.
  - `CLIENT_ID`: Obligatorio, viene desde el portal de Azure AD.
  - `redirectUri`: Pueden configurarse como nombre de paquete. No es necesario que se proporcione la `acquireToken` de llamadas.
  - `getUserInfo()`: Buscar si un usuario ya está en la caché la forma. El parámetro también describe cómo solicitar a un usuario si ese usuario no se encuentra o si el token de acceso del usuario no es válido. Este método se escribirá más adelante en esta guía.
  - `PromptBehavior.always`: Ayuda para pedir credenciales para omitir la memoria caché y cookies.
  - `Callback`: Llama después de un código de autorización se cambia por un símbolo. Tendrá un objeto `AuthenticationResult`, que contiene el token de acceso, la fecha de vencimiento y la información de token de ID.

> [AZURE.NOTE]  La aplicación del portal de empresa Intune Microsoft proporciona el componente de agente y es posible que esté instalado en el dispositivo del usuario. La aplicación proporciona acceso de (SSO) de inicio de sesión único en todas las aplicaciones en el dispositivo. Los desarrolladores deben estar preparados para permitir Intune. ADAL para Android usará la cuenta de agente si hay una cuenta de usuario que creó en el autenticador. Para usar el agente, el desarrollador debe registrar un especial `redirectUri` para el agente de usar. `redirectUri`se encuentra en el formato de msauth://packagename/Base64UrlencodedSignature. Puede obtener `redirectUri` para la aplicación mediante la secuencia de comandos `brokerRedirectPrint.ps1` o mediante la llamada a la API `mContext.getBrokerRedirectUri()`. La firma relacionada con los certificados de firma de la tienda Google Play.

 Puede omitir el usuario agente usando:

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```
> [AZURE.NOTE] Para reducir la complejidad de este tutorial B2C, nos hemos elegido para omitir al agente en nuestro ejemplo.

A continuación, crear métodos auxiliares que obtienen el token solo durante las llamadas de autenticación a la API de la tarea.

En la misma `ToDoActivity.java` de archivo, escriba lo siguiente.

```
    private void getToken(final AuthenticationCallback callback) {

        String policy = getIntent().getStringExtra("thePolicy");

        // one of the acquireToken overloads
        mAuthContext.acquireToken(ToDoActivity.this, Constants.SCOPES, Constants.ADDITIONAL_SCOPES,
                policy, Constants.CLIENT_ID, Constants.REDIRECT_URL, getUserInfo(),
                PromptBehavior.Always, "nux=1&" + Constants.EXTRA_QP, callback);
    }
```

También agregar métodos que "establecer" y "get" `AuthenticationResult` (que tiene el token) a la plantilla global `Constants`. Aunque `ToDoActivity.java` usa `sResult` en sus flujos, debe agregar estos métodos. Si no, las otras actividades no tienen acceso al token de trabajo (por ejemplo, agregar una tarea en `AddTaskActivity.java`).

```

    private AuthenticationResult getLocalToken() {
        return Constants.CURRENT_RESULT;
    }

    private void setLocalToken(AuthenticationResult newToken) {


        Constants.CURRENT_RESULT = newToken;
    }


```
## <a name="create-a-method-to-return-a-user-identifier"></a>Crear un método para devolver un identificador de usuario

ADAL para Android representa el usuario en el formulario de un `UserIdentifier` objeto. El usuario se administra. Puede usar el objeto para identificar si se usa el mismo usuario en las llamadas. Utilizando esta información, se puede confiar en la caché, en lugar de realizar una llamada de nueva al servidor. Para facilitar esta tarea, que se creó la `getUserInfo()` método que devuelve `UserIdentifier`. Puede usar esta con `acquireToken()`. También hemos creado un `getUniqueId()` método que devuelve el identificador de `UserIdentifier` en la memoria caché.

```
  private String getUniqueId() {
        if (sResult != null && sResult.getUserInfo() != null
                && sResult.getUserInfo().getUniqueId() != null) {
            return sResult.getUserInfo().getUniqueId();
        }

        return null;
    }

    private UserIdentifier getUserInfo() {

        final TextView names = (TextView)findViewById(R.id.userLoggedIn);
        String name = names.getText().toString();
        return new UserIdentifier(name, UserIdentifier.UserIdentifierType.OptionalDisplayableId);
    }

```

### <a name="write-helper-methods"></a>Escribir métodos auxiliares

A continuación, escriba algunos métodos auxiliares para borrar las cookies y proporcionar `AuthenticationCallback`. Estos métodos se usan puramente para los datos de ejemplo para asegurarse de que está en un estado limpio cuando llame a su `ToDo` actividad.

En el mismo archivo llamado `ToDoActivity.java`, escriba lo siguiente.

```

    private void clearSessionCookie() {

        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.removeSessionCookie();
        CookieSyncManager.getInstance().sync();
    }
```

```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        mAuthContext.onActivityResult(requestCode, resultCode, data);
    }

```   

## <a name="call-the-task-api"></a>Llamar a la API de tarea

Una vez su actividad listo para captar tokens, puede escribir su API para tener acceso al servidor de la tarea.

`getTasks`Proporciona una matriz que representa las tareas de su servidor.

Iniciar con `getTasks`.

En el mismo archivo llamado `ToDoActivity.java`, escriba lo siguiente.

```
    private void getTasks() {
        if (sResult == null || sResult.getToken().isEmpty())
            return;

        List<String> items = new ArrayList<>();
        try {
            items = new TodoListHttpService().getAllItems(sResult.getToken());
        } catch (Exception e) {
            items = new ArrayList<>();
        }

        ListView listview = (ListView) findViewById(R.id.listViewToDo);
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
                android.R.layout.simple_list_item_1, android.R.id.text1, items);
        listview.setAdapter(adapter);
    }

```

Escribir un método que inicializará las tablas en primera ejecución.

En el mismo archivo llamado `ToDoActivity.java`, escriba lo siguiente.

```
    private void initAppTables() {
        try {
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

        } catch (Exception e) {
            createAndShowDialog(new Exception(
                    "There was an error creating the Mobile Service. Verify the URL"), "Error");
        }
    }

```

Puede ver que este código requiere algunos métodos adicionales para hacer su trabajo. Escribir los siguiente.

### <a name="create-an-endpoint-url-generator"></a>Crear un generador de dirección URL del extremo

Es necesario generar la dirección URL del extremo que se va a conectar. Puede hacerlo en el mismo archivo de clase.

**En el mismo archivo** llamado `ToDoActivity.java`, escriba lo siguiente.

 ```
    private URL getEndpointUrl() {
        URL endpoint = null;
        try {
            endpoint = new URL(Constants.SERVICE_URL);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return endpoint;
    }

 ```


Tenga en cuenta que agregar el token de acceso a la convocatoria en el código de la siguiente sección.

## <a name="write-some-ux-methods"></a>Escribir métodos UX

Android requiere que tratar algunos devoluciones de llamada para trabajar con la aplicación. Estos son `createAndShowDialog` y `onResume()`. Esto es familiar si ha escrito código Android antes.

En el mismo archivo llamado `ToDoActivity.java`, escriba lo siguiente.

```
    @Override
    public void onResume() {
        super.onResume(); // Always call the superclass method first

        updateLoggedInUser();
        // User can click logout, it will come back here
        // It should refresh list again
        getTasks();
    }


```

A continuación, administrar sus devoluciones de llamada del cuadro de diálogo.

En el mismo archivo llamado `ToDoActivity.java`, escriba lo siguiente.

```
    /**
     * Creates a dialog and shows it
     *
     * @param exception The exception to show in the dialog
     * @param title     The dialog title
     */
    private void createAndShowDialog(Exception exception, String title) {
        createAndShowDialog(exception.toString(), title);
    }

    /**
     * Creates a dialog and shows it
     *
     * @param message The dialog message
     * @param title   The dialog title
     */
    private void createAndShowDialog(String message, String title) {
        AlertDialog.Builder builder = new AlertDialog.Builder(this);

        builder.setMessage(message);
        builder.setTitle(title);
        builder.create().show();
    }

```

Ahora debería tener un `ToDoActivity.java` que se compila. Todo el proyecto también debe compilar en este momento.

## <a name="run-the-sample-app"></a>Ejecute la aplicación de ejemplo

Por último, crear y ejecutar la aplicación en Studio Android o Eclipse. Inicio de sesión o cerrar sesión en la aplicación. Crear tareas para el usuario que ha iniciado sesión. Cerrar la sesión y vuelva a iniciarla como un usuario diferente y, a continuación, crear tareas para ese usuario.

Observe que las tareas están almacenados por usuario en la API, porque la API extrae la identidad del usuario que recibe el token de acceso.

Para referencia, en el ejemplo completo [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-Android/archive/complete.zip). También puede clonar desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-Android```


## <a name="important-information"></a>Información importante


### <a name="encryption"></a>Cifrado

ADAL cifra los tokens y la tienda en `SharedPreferences` de forma predeterminada. Puede mirar el `StorageHelper` clase para ver los detalles. Android presentó **AndroidKeyStore para 4.3(API18)** de almacenamiento seguro de claves privadas. ADAL usa para API18 y más. Si desea usar ADAL para versiones SDK inferiores, debe proporcionar una clave secreta en `AuthenticationSettings.INSTANCE.setSecretKey`.

### <a name="session-cookies-in-web-view"></a>Cookies de sesión en la vista web

Vista web Android desactive las cookies de sesión después de cerrar la aplicación. Se puede controlar con el siguiente código de ejemplo.
```
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
[Más información acerca de las cookies](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).
