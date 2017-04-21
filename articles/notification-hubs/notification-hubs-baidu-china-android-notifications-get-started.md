<properties
    pageTitle="Introducción a Azure Hubs de notificación utilizando Baidu | Microsoft Azure"
    description="En este tutorial, aprenderá a usar Hubs de notificación de Azure para las notificaciones de inserción a dispositivos Android con Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="08/19/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-using-baidu"></a>Introducción a Hubs de notificación utilizando Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Información general

Inserción de nube de Baidu es un servicio de nube chino que puede usar para enviar notificaciones de inserción a dispositivos móviles. Este servicio es especialmente útil en China, donde la entrega de notificaciones de inserción para Android es compleja debido a la presencia de aplicación diferentes almacena y servicios de inserción, además de la disponibilidad de los dispositivos Android que no están conectados normalmente a GCM (mensajería de nube de Google).

##<a name="prerequisites"></a>Requisitos previos

Este tutorial requiere lo siguiente:

+ Android SDK (suponemos que va a utilizar Eclipse), que puede descargarse desde el <a href="http://go.microsoft.com/fwlink/?LinkId=389797">sitio de Android</a>
+ [SDK de servicios móviles Android]
+ [Inserción de Baidu Android SDK]

>[AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##<a name="create-a-baidu-account"></a>Crear una cuenta de Baidu

Para utilizar Baidu, debe tener una cuenta de Baidu. Si ya tiene una, inicie sesión en el [portal de Baidu] y vaya al paso siguiente. En caso contrario, vea las siguientes instrucciones sobre cómo crear una cuenta de Baidu.  

1. Vaya al [portal de Baidu] y haga clic en el vínculo de**登录**(**Inicio de sesión**). Haga clic en**立即注册**para iniciar el proceso de registro de cuenta.

    ![][1]

2. Especifique los detalles necesarios: teléfono/código de verificación, la contraseña y la dirección de correo y haga clic en **el registro**.

    ![][2]

3. Se le enviará un correo electrónico a la dirección de correo electrónico que haya escrito con un vínculo para activar su cuenta de Baidu.

    ![][3]

4. Inicie sesión en su cuenta de correo electrónico, abra el correo de activación de Baidu y haga clic en el vínculo de activación para activar su cuenta de Baidu.

    ![][4]

Una vez que tenga una cuenta de Baidu activada, inicie sesión en el [portal de Baidu].

##<a name="register-as-a-baidu-developer"></a>Registrar como programador Baidu

1. Una vez que haya iniciado sesión el [portal de Baidu], haga clic en**更多 >>** (**más**).

    ![][5]

2. Desplácese hacia abajo en la sección**站长与开发者服务 (Administrador del sitio Web y servicios de programador)** y haga clic en**百度开放云平台**(**Baidu abrir plataforma de nube**).

    ![][6]

3. En la siguiente página, haga clic en**开发者服务**(**Developer Services**) en la esquina superior derecha.

    ![][7]

4. En la siguiente página, haga clic en**注册开发者**(**Programadores registrados**) desde el menú en la esquina superior derecha.

    ![][8]

5. Escriba su nombre, una descripción y un número de teléfono móvil para recibir un mensaje de texto de verificación y, a continuación, haga clic en**送验证码**(**Código de verificación Enviar**). Tenga en cuenta que los números de teléfono internacionales, necesita entre paréntesis el código de país. Por ejemplo, para un número de Estados Unidos, será **(1) 1234567890**.

    ![][9]

6. A continuación, debe recibir un mensaje de texto con un número de verificación, tal como se muestra en el ejemplo siguiente:

    ![][10]

7. Escriba el número de verificación del mensaje en**验证码**(**código de confirmación**).

8. Por último, complete el registro del programador acepte el contrato de Baidu y haciendo clic en**提交**(**Enviar**). Verá la siguiente página completar correctamente el registro:

    ![][11]

##<a name="create-a-baidu-cloud-push-project"></a>Crear un proyecto de inserción de nube de Baidu

Cuando se crea un proyecto de inserción de nube Baidu, recibe el identificador de aplicación, la clave de API y secreto.

1. Una vez que haya iniciado sesión el [portal de Baidu], haga clic en**更多 >>** (**más**).

    ![][5]

2. Desplácese hacia abajo en la sección**站长与开发者服务**(**Administrador del sitio Web y servicios de programador**) y haga clic en**百度开放云平台**(**Baidu abrir plataforma de nube**).

    ![][6]

3. En la siguiente página, haga clic en**开发者服务**(**Developer Services**) en la esquina superior derecha.

    ![][7]

4. En la siguiente página, haga clic en**云推送**(**Push de nube**) de la sección**云服务**(**Servicios de nube**).

    ![][12]

5. Una vez un desarrollador registrado, verá**管理控制台**(**Consola de administración**) en el menú superior. Haga clic en**开发者服务管理**(**administración de servicios de programadores**).

    ![][13]

6. En la siguiente página, haga clic en**创建工程**(**Crear proyecto**).

    ![][14]

7. Escriba un nombre de aplicación y haga clic en**创建**(**crear**).

    ![][15]

8. Tras la creación correcta de un proyecto de inserción de nube Baidu, verá una página con **ID**, **Clave API**y **Clave secreta**. Tome nota de la API clave y secreto, que usará más adelante.

    ![][16]

9. Configurar el proyecto para las notificaciones de inserción haciendo clic en**云推送**(**Inserción de nube**) en el panel izquierdo.

    ![][31]

10. En la siguiente página, haga clic en el botón**推送设置**(**configuración de inserción**).

    ![][32]  

11. En la página Configuración, agregue el nombre del paquete que se pueden usar en su proyecto Android en el campo**应用包名**(**paquete de aplicación**) y, a continuación, haga clic en**保存设置**(**Guardar**).  

    ![][33]

Verá la**保存成功!** (**guardado correctamente!**) mensaje.

##<a name="configure-your-notification-hub"></a>Configurar su centro de notificación

1. Inicie sesión en el [Portal de Azure clásica]y, a continuación, haga clic en **+ nuevo** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicación**, haga clic en **Bus de servicio**, haga clic en **El Hub de notificación**y, a continuación, haga clic en **Crear rápido**.

3. Proporcione un nombre para el **Concentrador de notificación**, seleccione la **región** y el **Namespace** donde se creará este concentrador de notificación y, a continuación, haga clic en **crear un nuevo concentrador de notificación**.  

    ![][17]

4. Haga clic en el espacio de nombres en el que ha creado su centro de notificación y, a continuación, haga clic en **Hubs de notificación** en la parte superior.

    ![][18]

5. Seleccione el hub de notificación que ha creado y, a continuación, haga clic en **Configurar** en el menú superior.

    ![][19]

6. Desplácese hacia abajo hasta la sección **configuración de notificación de baidu** y escriba la clave de API y secreto que obtuvo desde la consola de Baidu previamente para el proyecto de inserción de nube de Baidu. Haga clic en **Guardar**.

    ![][20]

7. Haga clic en la pestaña **panel** en la parte superior para el concentrador de notificación y, a continuación, haga clic en **Vista de cadena de conexión**.

    ![][21]

8. Tome nota de los **DefaultListenSharedAccessSignature** y **DefaultFullSharedAccessSignature** desde la ventana de **información de conexión de acceso** .

    ![][22]

##<a name="connect-your-app-to-the-notification-hub"></a>Conectar la aplicación al concentrador de notificación

1. En Eclipse ADT, cree un nuevo proyecto Android (**archivo** > **nuevo** > **Android proyecto de la aplicación**).

    ![][23]

2. Escriba un **Nombre de la aplicación** y asegúrese de que la versión **Mínima necesaria SDK** se establece en **API 16: Android 4.1**.

    ![][24]

3. Haga clic en **siguiente** y siga el asistente hasta que aparezca la ventana **Crear actividad** . Asegúrese de que está seleccionada la **Actividad en blanco** y, por último, seleccione **Finalizar** para crear una nueva aplicación Android.

    ![][25]

4. Asegúrese de que el **Destino de compilación de proyecto** está configurado correctamente.

    ![][26]

5. Descargue el archivo 0.4.jar de hubs de notificación en la pestaña **archivos** de [Notificación-Hubs-Android-SDK en Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Agregue el archivo a la carpeta de **bibliotecas** de su proyecto Eclipse y actualice la carpeta de *bibliotecas* .

6. Descargar y descomprima el [SDK de Baidu Push Android], abra la carpeta de **bibliotecas** y a continuación, copie el archivo jar **x.y.z pushservice** y la **armeabi** & **mips** carpetas en la carpeta de **bibliotecas** de la aplicación Android.

7. Abra el archivo **AndroidManifest.xml** del proyecto Android y agregar los permisos necesarios para el SDK Baidu.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Agregar la propiedad **android: nombre** para el elemento de **aplicación** en **AndroidManifest.xml**, reemplazar *yourprojectname* (por ejemplo, **com.example.BaiduTest**). Asegúrese de que el nombre del proyecto coincide con uno que configuró en la consola de Baidu.

        <application android:name="yourprojectname.DemoApplication"

9. Agregue la siguiente configuración en el elemento de aplicación después de la **. Actividad principal** elemento de la actividad, reemplazar *yourprojectname* (por ejemplo, **com.example.BaiduTest**):

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Agregar una nueva clase denominada **ConfigurationSettings.java** al proyecto.

    ![][28]

    ![][29]

10. Agregue el código siguiente en él:

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    Establezca el valor de **API_KEY** con lo que ha recuperado Baidu nube del proyecto de una versión anterior, **NotificationHubName** con su nombre de concentrador de notificación desde el Portal de clásico de Azure y **NotificationHubConnectionString** con DefaultListenSharedAccessSignature desde el Portal de clásico de Azure.

11. Agregar una nueva clase denominada **DemoApplication.java**y agregarle el siguiente código:

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Agregue otra nueva clase llamado **MyPushMessageReceiver.java**y agregue el código siguiente en él. Esta es la clase que controla las notificaciones de inserción que se reciben desde el servidor de inserción Baidu.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Abra **MainActivity.java**y agregue lo siguiente al método **onCreate** :

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Abrir las siguientes afirmaciones importar en la parte superior:

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##<a name="send-notifications-to-your-app"></a>Enviar notificaciones a su aplicación


Puede probar rápidamente recibir notificaciones en su aplicación mediante el envío de notificaciones en el [Portal de Azure](https://portal.azure.com/) mediante el botón **Probar enviar** en el hub de notificación, como se muestra en la pantalla siguiente.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Normalmente se envían las notificaciones de inserción en un servicio de back-end como servicios móviles o ASP.NET mediante una biblioteca compatible. También puede usar la API de REST directamente al enviar mensajes de notificación si no está disponible para el fondo de una biblioteca.

En este tutorial, se Simplifique y se muestran solo probar la aplicación cliente mediante el envío de notificaciones mediante .NET SDK para hubs de notificación en una aplicación de consola en lugar de un servicio de back-end. Se recomienda el tutorial de [Uso Hubs de notificación para las notificaciones de inserción a los usuarios](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) como el siguiente paso para enviar notificaciones de un back-end ASP.NET. Sin embargo, se pueden utilizar los siguientes enfoques para enviar notificaciones:

* **Interfaz REST**: admite la notificación en cualquier plataforma de back-end mediante la [interfaz REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure notificación Hubs .NET SDK**: en el Nuget paquete Manager para Visual Studio, ejecutar [Microsoft.Azure.NotificationHubs de paquete de instalación](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [cómo usar Hubs de notificación de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

* **Aplicaciones móviles**: para obtener un ejemplo de cómo enviar notificaciones de un servidor de aplicaciones de Azure aplicación servicio Mobile se integra con Hubs de notificación, vea [Agregar las notificaciones de inserción a la aplicación móvil](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java o PHP**: para obtener un ejemplo de cómo enviar notificaciones mediante las API de REST, consulte "Cómo usar Hubs de notificación de Java y PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

##<a name="optional-send-notifications-from-a-net-console-app"></a>(Opcional) Enviar notificaciones de una aplicación de consola de .NET.

En esta sección, le mostraremos enviar una notificación con una aplicación de consola de .NET.

1. Crear una nueva aplicación de consola de Visual C#:

    ![][30]

2. En la ventana de la consola del Administrador de paquetes, establecer **predeterminado de proyecto** para el nuevo proyecto de aplicación de consola y, a continuación, en la ventana de la consola, ejecute el siguiente comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esto agrega una referencia en el SDK de Hubs de notificación de Azure utilizando el <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">paquete Microsoft.Azure.Notification Hubs NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Abra el archivo **Program.cs** y agregue la siguiente instrucción using:

        using Microsoft.Azure.NotificationHubs;

4. En la `Program` de clase, agregue el método siguiente y reemplace *DefaultFullSharedAccessSignatureSASConnectionString* y *NotificationHubName* con los valores que tiene.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Agregue las siguientes líneas en el método **Main** :

         SendNotificationAsync();
         Console.ReadLine();

##<a name="test-your-app"></a>Probar la aplicación

Para probar esta aplicación con un teléfono real, simplemente conecte el teléfono a su equipo mediante un cable USB. Cargar la aplicación en el teléfono adjunto.

Para probar esta aplicación con el emulador, en la barra de herramientas superior de Eclipse, haga clic en **Ejecutar**y, a continuación, seleccione la aplicación. Se inicia el emulador y, a continuación, se carga y se ejecuta la aplicación.

La aplicación recupera los 'ID' y '///ChannelID' desde el servicio de notificación de inserción de Baidu y se registra con el hub de notificación.

Para enviar una notificación de prueba, que puede usar la ficha Depurar del Portal clásico de Azure. Si ha creado la aplicación de consola de .NET para Visual Studio, solo tiene que presionar la tecla F5 en Visual Studio para ejecutar la aplicación. La aplicación le enviará una notificación que desee que aparezca en el área de notificación superior de su dispositivo o emulador.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[SDK de servicios móviles Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Inserción de Baidu Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Portal de clásico de Azure]: https://manage.windowsazure.com/
[Portal de Baidu]: http://www.baidu.com/
