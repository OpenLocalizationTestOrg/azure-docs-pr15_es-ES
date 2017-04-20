
1. En el [portal de Azure](https://portal.azure.com/), haga clic en **Examinar todos** > **Servicios de aplicación** > su aplicación móvil de back-end. En **configuración**, haga clic en la **Aplicación de servicio de inserción**y después haga clic en el nombre del concentrador de notificación.

2. Vaya a **Windows (WNS)**, escriba la **clave de seguridad** (secreto de cliente) y el **Paquete SID** que obtuvo desde el sitio de servicios de Live y haga clic en **Guardar**.

    ![Establecer la clave WNS en el portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

El back-end ahora está configurado para utilizar WNS para enviar notificaciones de inserción.
