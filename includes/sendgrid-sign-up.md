Los clientes de Azure pueden desbloquear 25.000 correos electrónicos gratuitos cada mes. Estos mensajes mensuales gratuitos 25.000 tendrá acceso a informes y el análisis y [todas las API][] (Web, SMTP, eventos, análisis y más). Para obtener información acerca de los servicios adicionales proporcionados por SendGrid, consulte la página [Características de SendGrid][] .

### <a name="to-sign-up-for-a-sendgrid-account"></a>Para suscribirse a una cuenta de SendGrid

1. Inicie sesión en el [Portal de administración de Azure][].

2. En el panel inferior del portal de administración, haga clic en **nuevo**.

    ![nuevo comando barra][command-bar-new]

3. Haga clic en **catálogo de soluciones**.

    ![sendgrid almacén][sendgrid-store]

4. En el cuadro de diálogo **Elegir una aplicación y el servicio** , seleccione **SendGrid** y haga clic en la flecha derecha.

5. En el cuadro de diálogo **Personalizar aplicación y servicio** seleccione el plan de **SendGrid** que desea suscribirse.

6. Escriba un nombre para identificar el servicio de **SendGrid** en la configuración de Azure o use el valor predeterminado de **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Nombres deben estar comprendido entre 1 y 100 caracteres de longitud y contienen caracteres de subrayado, guiones, puntos y sólo caracteres alfanuméricos. El nombre debe ser único en la lista de artículos de la tienda de Azure suscritas.

    ![pantalla de la tienda de 2][store-screen-2]

7. Elegir un valor de la región; Por ejemplo, US oeste.

8. Haga clic en la flecha derecha.

9. En la pestaña **Revisar de la compra** , revise el plan y la información de precios y revise las condiciones legales. Si acepta los términos, haga clic en la marca de verificación. Después de hacer clic en la marca de verificación, la cuenta SendGrid iniciará el [proceso de aprovisionamiento de SendGrid].

    ![pantalla de la tienda de 3][store-screen-3]

10. Después de confirmar la compra se le redirigirá a los paneles de complementos y verá el mensaje **SendGrid de compra**.

    ![mensaje de compras sendgrid][sendgrid-purchasing-message]

    Su cuenta de SendGrid se aprovisiona inmediatamente y verá el mensaje **comprado correctamente SendGrid del complemento**. Ahora se crean su cuenta y las credenciales. Ya está listo para enviar mensajes de correo electrónico en este momento. 

    Para modificar el plan de suscripción o consulte la SendGrid póngase en contacto con la configuración, haga clic en el nombre de su servicio SendGrid para abrir el panel de SendGrid Marketplace. 

    ![sendgrid-agregar-en-panel][sendgrid-add-on-dashboard]

    Para enviar un correo electrónico usando SendGrid, debe proporcionar las credenciales de cuenta (nombre de usuario y contraseña).

### <a name="to-find-your-sendgrid-credentials"></a>Para buscar las credenciales SendGrid ###

1. Haga clic en **información de conexión**.

    ![sendgrid-conexión-botón de información][sendgrid-connection-info-button]

2. En el cuadro de diálogo *información de conexión* , copie el nombre de usuario y la **contraseña** para usar más adelante en este tutorial.

    ![información de conexión sendgrid][sendgrid-connection-info]

    Para configurar el correo electrónico entregabilidad, haga clic en el botón **Administrar** . Esto le redirigirá a su Panel de Control SendGrid. 

    ![panel de control de sendgrid][sendgrid-control-panel]

    Para obtener más información sobre cómo empezar con SendGrid, consulte [Introducción a SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Características de SendGrid]: http://sendgrid.com/features
[Portal de administración de Azure]: https://manage.windowsazure.com
[SendGrid Introducción]: http://sendgrid.com/docs
[Proceso de aprovisionamiento de SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[todas las API]: https://sendgrid.com/docs/API_Reference/index.html

