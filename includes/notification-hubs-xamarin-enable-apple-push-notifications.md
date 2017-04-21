

Para registrar la aplicación para las notificaciones de inserción a través de servicio de notificación de inserción Apple (APN), debe crear un nuevo certificado de inserción, el identificador de aplicación y aprovisionamiento perfil para el proyecto en el portal de programadores de Apple. El identificador de aplicación contiene los valores de configuración que permiten la aplicación enviar y recibir notificaciones de inserción. Esta configuración incluye el certificado de notificación de inserción necesario para autenticar con Apple inserción notificación servicio (APN) al enviar y recibir notificaciones de inserción. Para obtener más información sobre estos conceptos, consulte la documentación del [Servicio de notificación de inserción de Apple](http://go.microsoft.com/fwlink/p/?LinkId=272584) oficial.


####<a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Generar el archivo de solicitud de firma de certificado para el certificado de inserción

Estos pasos le guiará a través de la creación de la solicitud de firma del certificado. Se utilizará para generar un certificado de inserción para utilizar con APN.

1. En el Mac, ejecute la herramienta de acceso a llaves. Se puede abrir desde la carpeta **utilidades** o la **otra** carpeta en el panel de inicio.

2. Haga clic en **Acceso a llaves**, expanda **Ayudante de certificado**y haga clic en **Solicitar un certificado de una entidad emisora de certificados...**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Seleccione su **Dirección de correo electrónico de usuario** y el **Nombre común** , asegúrese de que está seleccionada la opción **Guardar en disco** y, a continuación, haga clic en **continuar**. Deje el campo **Dirección de correo electrónico de la entidad emisora de certificados** que no sea necesario.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Escriba un nombre para el archivo de solicitud de firma de certificado (CSR) en el cuadro **Guardar como**, seleccione la ubicación en **donde**, a continuación, haga clic en **Guardar**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Esto guarda el archivo CSR en la ubicación seleccionada; la ubicación predeterminada se encuentra en el escritorio. Recuerde que la ubicación elegida para este archivo.


####<a name="register-your-app-for-push-notifications"></a>Registrar su aplicación para las notificaciones de inserción

Crear un nuevo ID de aplicación explícitas de la aplicación con Apple y también se configure para las notificaciones de inserción.  

1. Desplácese hasta el [Portal de aprovisionamiento de iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) en el centro de desarrollo de Apple, inicie sesión con su ID de Apple, haga clic en **los identificadores**, a continuación, haga clic en **Los identificadores de aplicación**y por último, haga clic en el **+** iniciar sesión registrar una nueva aplicación.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Actualice los tres campos siguientes para la nueva aplicación y, a continuación, haga clic en **continuar**:

    * **Nombre**: escriba un nombre descriptivo para la aplicación en el campo **nombre** de la sección **Descripción de ID de aplicación** .

    * **Identificador de paquete**: en la sección **Explícitas ID de aplicación** , escriba un **Identificador de paquete** en el formulario `<Organization Identifier>.<Product Name>` como se mencionó en la [Guía de distribución de la aplicación](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Debe coincidir con lo que también se usa en el proyecto XCode, Xamarin o Cordova para la aplicación.

    * **Notificaciones Push**: Active la opción de **Notificaciones de inserción** en la sección de **Servicios de aplicación** .

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.  En Confirme su pantalla de ID de aplicación, revise la configuración y después de haber comprobado haga clic en **Enviar**

4.  Una vez que ha enviado el nuevo identificador de aplicación, se muestra la pantalla de **registro completo** . Haga clic en **Listo**.

5. En el Centro para desarrolladores de identificadores de aplicación, busque el identificador de aplicación que acaba de crear y haga clic en la fila. Al hacer clic en la fila de ID de aplicación mostrará los detalles de la aplicación. Haga clic en el botón **Editar** en la parte inferior.

6. Desplácese hasta la parte inferior de la pantalla y haga clic en el botón **Crear certificado** en la sección **Certificado SSL de inserción de desarrollo**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Esta opción mostrará al Asistente "Agregar iOS certificado".

    > [AZURE.NOTE] En este tutorial se usa un certificado de desarrollo. Cuando se registra un certificado de producción, se utiliza el mismo proceso. Asegúrese de usar el mismo tipo de certificado al enviar notificaciones.

7. Haga clic en **Seleccionar archivo**, busque la ubicación donde guardó el CSR para el certificado de inserción. A continuación, haga clic en **Generar**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Una vez creado el portal el certificado, haga clic en el botón **Descargar** .

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Esto descarga el certificado de firma y guarda en el equipo en la carpeta de descargas.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] De forma predeterminada, el archivo descargado un certificado de desarrollo se denomina **aps_development.cer**.

9. Haga doble clic en el certificado de inserción descargado **aps_development.cer**. Esto instala el nuevo certificado en las llaves, tal como se muestra a continuación:

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] El nombre de su certificado puede ser diferente, pero se precedido **desarrollo de Apple iOS servicios Push:**.

10. En acceso a llaves, haga clic en el nuevo certificado de inserción que acaba de crear en la categoría de **certificados** . Haga clic en **Exportar**, nombre del archivo, seleccione el formato **. p12** y, a continuación, haga clic en **Guardar**.

    Recuerde que el nombre de archivo y la ubicación del certificado push. p12 exportado. Se utilizará para habilitar la autenticación con APN cargándolo en el Portal de clásico de Azure.



####<a name="create-a-provisioning-profile-for-the-app"></a>Crear un perfil de aprovisionamiento para la aplicación

1. En el <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de aprovisionamiento de iOS</a>, seleccione **Perfiles de aprovisionamiento**, seleccionar **todo**y, a continuación, haga clic en el **+** botón para crear un nuevo perfil. Esto inicia al asistente **Agregar iOS Provisiong perfil**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Seleccione **iOS desarrollo de aplicaciones** en **desarrollo** como el tipo de perfil provisiong y haga clic en **continuar**.


3. A continuación, seleccione el ID de aplicación que acaba de crear de la lista desplegable de **ID de aplicación** y haga clic en **continuar**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. En la pantalla **Seleccionar certificados** , seleccione el certificado de desarrollo utilizado para firmar el código y haga clic en **continuar**. Se trata de un certificado de firma, no el certificado de inserción que acaba de crear.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. A continuación, seleccione los **dispositivos** para probar y haga clic en **continuar**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Por último, elija un nombre para el perfil en **Nombre del perfil**, haga clic en **Generar**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
