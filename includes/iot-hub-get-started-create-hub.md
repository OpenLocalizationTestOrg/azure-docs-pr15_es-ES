## <a name="create-an-iot-hub"></a>Crear un concentrador IoT

Crear un concentrador IoT para su dispositivo simulado para conectarse a. Los pasos siguientes muestran cómo completar esta tarea a través del portal de Azure.

1. Inicie sesión en el [portal de Azure][lnk-portal].

2. En el Jumpbar, haga clic en **nuevo** > **Internet de cosas** > **Azure IoT concentrador**.

    ![Portal de Azure Jumpbar][1]

3. En el módulo **IoT concentrador** , elija la configuración para su centro IoT.

    ![Módulo de concentrador IoT][2]

    * En el cuadro **nombre** , escriba un nombre para su centro IoT. Si el **nombre** es válido y disponible, aparece una marca de verificación verde en el cuadro **nombre** .
    * Seleccione un [nivel de precios y escala][lnk-pricing]. Este tutorial no requiere un nivel específico. Para este tutorial, use el nivel de F1 gratuito.
    * En el **grupo de recursos**, cree un nuevo grupo de recursos o seleccione uno existente. Para obtener más información, vea [uso de grupos de recursos para administrar los recursos de Azure][lnk-resource-groups].
    * En **ubicación**, seleccione la ubicación para hospedar su centro IoT. En este tutorial, elija la ubicación más cercana.

4. Cuando haya elegido el concentrador IoT opciones de configuración, haga clic en **crear**.  Este proceso puede demorarse unos minutos para Azure crear su centro IoT. Para comprobar el estado, puede supervisar el progreso de la Startboard o en el panel de notificaciones.

    ![Nuevo estado de concentrador IoT][3]

5. Cuando el concentrador IoT se ha creado correctamente, haga clic en el icono nuevo para su centro IoT en el portal de Azure para abrir el módulo para el nuevo concentrador IoT. Anote el **nombre de host**y, a continuación, haga clic en **directivas de acceso compartido**.

    ![Nuevo módulo de concentrador IoT][4]

6. En el módulo de **directivas de acceso compartido** , haga clic en la directiva **iothubowner** y, a continuación, copie y tome nota de la cadena de conexión en el módulo **iothubowner** . Para obtener más información, vea [control de acceso] [ lnk-access-control] la "Guía del programador Azure concentrador IoT".

    ![Módulo de directivas de acceso compartido][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
