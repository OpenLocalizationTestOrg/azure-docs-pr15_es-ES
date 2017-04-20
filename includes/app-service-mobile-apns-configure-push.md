

1. En el Mac, inicie **Acceso a llaves**. Abra **Mis certificados** en **categoría** en la barra izquierda navigationn. Busque el certificado SSL que ha descargado en la sección anterior y revelar su contenido. Seleccione solo el certificado (no seleccione la clave privada) y [Exportar](https://support.apple.com/kb/PH20122?locale=en_US).

2. En el [portal de Azure](https://portal.azure.com/), haga clic en **Examinar todos** > **Servicios de aplicación** > su aplicación móvil de back-end. En **configuración**, haga clic en la **Aplicación de servicio de inserción**y después haga clic en el nombre del concentrador de notificación. Vaya a **Servicios de notificación de inserción de Apple** > **cargar el certificado**. Cargue el archivo. p12, seleccionar el correcto **modo** (dependiendo de si su cliente SSL de certificados de versiones anteriores es de producción o espacio aislado). Guarde los cambios.

¡Ahora, el servicio está configurado para trabajar con las notificaciones de inserción en iOS!

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
