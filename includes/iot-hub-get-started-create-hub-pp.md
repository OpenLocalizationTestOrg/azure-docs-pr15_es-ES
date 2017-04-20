## <a name="create-a-device-management-enabled-iot-hub"></a>Crear un dispositivo concentrador IoT habilitada la administración

Puesto que la administración de dispositivos de concentrador IoT está en vista previa, tiene que crear un concentrador de IoT dispositivo administración habilitada. Cuando la administración de dispositivos de concentrador IoT alcanza la disponibilidad General, este tutorial se actualizará. Los pasos siguientes muestran cómo completar esta tarea con el portal de Azure.

1.  Inicie sesión en el [portal de Azure].
2.  En el Jumpbar, haga clic en **nuevo**, a continuación, haga clic en **Internet de elementos**y, a continuación, haga clic en **Azure IoT concentrador**.

    ![][img-new-hub]

3.  En el módulo **IoT concentrador** , elija la configuración para su centro IoT.

    ![][img-configure-hub]

  -   En el cuadro **nombre** , escriba un nombre para su centro IoT. Si el **nombre** es válido y disponible, aparece una marca de verificación verde en el cuadro **nombre** .
  -   Seleccione un **nivel de precios y escala**. Este tutorial no requiere un nivel específico.
  -   En el **grupo de recursos**, cree un nuevo grupo de recursos o seleccione uno existente. Para obtener más información, vea [uso de grupos de recursos para administrar los recursos de Azure].
  -   Active la casilla para **Habilitar la administración de dispositivos - vista previa**.
  -   En **ubicación**, seleccione la ubicación para hospedar el concentrador IoT. Administración de dispositivos de concentrador IoT solo está disponible en Estados Unidos oriental, Norte Europa y Asia Oriental durante la vista previa público.

    > [AZURE.NOTE]Si no activa la casilla para **Habilitar la administración de dispositivos**, los ejemplos no funcionan.<br/>Comprobación de **Habilitar la administración de dispositivos**, crear una vista previa IoT concentrador solo admite oriental de Estados Unidos, Europa del Norte y Asia oriental y no está pensado para escenarios de producción. No puede migrar dispositivos dentro y fuera de hubs habilitada de administración de dispositivos.

4.  Cuando haya elegido el concentrador IoT opciones de configuración, haga clic en **crear**. Este proceso puede demorarse unos minutos para Azure crear su centro IoT. Para comprobar el estado, puede supervisar el progreso de la **Startboard** o en el panel de **notificaciones** .

    ![][img-monitor]

5.  Cuando el concentrador IoT se ha creado correctamente, se abrirá automáticamente el módulo para su centro. Anote el **nombre de host**y, a continuación, haga clic en **directivas de acceso compartido**.

    ![][img-keys]

6.  Haga clic en la directiva **iothubowner** , a continuación, copiar y anote la cadena de conexión en el módulo **iothubowner** . Copiar en una ubicación que se puede acceder a más tarde porque se necesita para completar este tutorial.

    > [AZURE.NOTE] En escenarios de producción, asegúrese de evitar el uso de las credenciales de **iothubowner** .

    ![][img-connection]

Ahora ha creado un dispositivo concentrador IoT habilitada la administración. Necesita la cadena de conexión para completar este tutorial.

## <a name="create-a-device-identity"></a>Crear una identidad de dispositivo

En esta sección, utilice una herramienta de nodo denominada [IoT concentrador Explorer] [ iot-hub-explorer] para crear una identidad de dispositivo para este tutorial.

1. En su entorno de línea de comandos, ejecute lo siguiente:

    instalación de NPM -giothub-explorer@latest

2. A continuación, ejecute el comando siguiente para iniciar sesión en su centro, recordarlo sustituir `{service connection string}` con la cadena de conexión de IoT Hub copiado previamente:

    inicio de sesión de explorador iothub "{cadena de conexión de servicio}"

3. Por último, crear una nueva identidad de dispositivo denominada `myDeviceId` con el comando:

    Explorador de iothub crear myDeviceId--cadena de conexión

Anote la cadena de conexión de dispositivo en el resultado. Esta cadena de conexión se utiliza por la aplicación de dispositivo para conectarse a su centro IoT como un dispositivo.

![][img-identity]

Consulte [Introducción a IoT concentrador] [ lnk-getstarted] para una forma de crear las identidades de dispositivo mediante programación.

<!-- images and links -->
[img-new-hub]: media/iot-hub-get-started-create-hub-pp/image1.png
[img-configure-hub]: media/iot-hub-get-started-create-hub-pp/image2.png
[img-monitor]: media/iot-hub-get-started-create-hub-pp/image3.png
[img-keys]: media/iot-hub-get-started-create-hub-pp/image4.png
[img-connection]: media/iot-hub-get-started-create-hub-pp/image5.png
[img-identity]: media/iot-hub-get-started-create-hub-pp/devidentity.png

[Portal de Azure]: https://portal.azure.com/
[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[Uso de grupos de recursos para administrar los recursos de Azure]: ../articles/azure-portal/resource-group-portal.md
