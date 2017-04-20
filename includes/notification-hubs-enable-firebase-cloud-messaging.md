

1. Inicie sesión en la [consola de Firebase](https://firebase.google.com/console/). Crear un nuevo proyecto de Firebase si todavía no tiene uno.
2. Una vez creado el proyecto haga clic en **Agregar Firebase a su aplicación Android** y siga las instrucciones proporcionadas.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. En la consola Firebase, haga clic en la Web para el proyecto y, a continuación, haga clic en **Configuración del proyecto**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Haga clic en la pestaña **Mensajería de nube** en la configuración del proyecto y copie el valor de la **clave de servidor** y el **Identificador del remitente**.  Estos valores se usarán más adelante para configurar la directiva de acceso de concentrador de notificación y el controlador de notificación en la aplicación.
  