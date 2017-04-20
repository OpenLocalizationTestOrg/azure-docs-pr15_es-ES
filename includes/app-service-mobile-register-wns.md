
1. En el Explorador de soluciones de Visual Studio, haga clic en el proyecto de aplicación de la tienda de Windows, haga clic en **almacén** > **Asociar la aplicación en la tienda …**.

    ![Asociar la aplicación con la tienda Windows](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)

2. En el asistente, haga clic en **siguiente**, inicie sesión con su cuenta de Microsoft, escriba un nombre para la aplicación de **reserva un nuevo nombre de la aplicación**y haga clic en **reservar**.

3. Una vez creado correctamente el registro de aplicación, seleccione el nuevo nombre de la aplicación, haga clic en **siguiente**y, a continuación, haga clic en **asociar**. Esto agrega la información de registro de la tienda Windows necesaria al manifiesto de aplicación.

7. Repita los pasos 1 y 3 para el proyecto de aplicación de la tienda de Windows Phone con el mismo registro que creó anteriormente para la aplicación de la tienda de Windows.  

7. Vaya al [Centro de desarrollo de Windows](https://dev.windows.com/en-us/overview), iniciar sesión con su cuenta de Microsoft, haga clic en el nuevo registro de aplicación en **Mis aplicaciones**, expanda **Servicios** > **notificaciones de inserción**.

8. En la página de **notificaciones de inserción** , haga clic en **el sitio de servicios de Live** en **Servicios de notificación de inserción de Windows (WNS) y aplicaciones de Microsoft Azure Mobile**y tome nota de los valores del **Paquete SID** y el valor *actual* de **Secreto de aplicación**. 

    ![Configuración de la aplicación en el centro de desarrollo](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] El secreto de la aplicación y el paquete SID son las credenciales de seguridad importantes. No compartir estos valores con cualquier persona o distribuir con la aplicación.
