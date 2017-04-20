## <a name="view-device-telemetry-in-the-dashboard"></a>Vista de telemetría de dispositivo en el tablero de mandos

El tablero de mandos en la solución de supervisión remota le permite ver la telemetría que los dispositivos envían a IoT concentrador.

1. En el explorador, vuelva al panel de solución de monitoreo remoto, haga clic en **dispositivos** en el panel izquierdo para ir a la **lista de dispositivos**.

2. En la **lista de dispositivos**, verá que el estado del dispositivo ahora es en **ejecución**.

    ![][18]

3. Haga clic en **escritorio** para volver al panel, seleccione el dispositivo en el **dispositivo a la vista** desplegable para ver su telemetría. La telemetría desde la aplicación de ejemplo es 50 unidades de temperatura interna, 55 unidades de temperatura exterior y 50 unidades de humedad. Observe que, de forma predeterminada el tablero de mandos muestra sólo los valores de temperatura y humedad.

    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Enviar un comando al dispositivo

El tablero de mandos en la solución de supervisión remota le permite enviar comandos a los dispositivos a través de concentrador IoT. Por ejemplo, en la solución de supervisión remota puede enviar un comando para establecer la temperatura interna de un dispositivo.

1. En el panel de supervisión remota solución, haga clic en **dispositivos** en el panel izquierdo para ir a la **lista de dispositivos**.

2. Haga clic en **ID de dispositivo** para el dispositivo en la **lista de dispositivos**.

3. En el panel de **Detalles del dispositivo** , haga clic en **comandos**.

    ![][13]

4. En el menú desplegable **comandos** , seleccione **SetTemperature**y escriba un nuevo valor de temperatura en la **temperatura** . Haga clic en el **comando Send** para enviar el comando al dispositivo.

    ![][14]

    > [AZURE.NOTE] Inicialmente, el historial de comandos muestra el estado de comando como **pendiente**. Cuando el dispositivo reconoce el comando, el estado cambia para el **éxito**.

5. En el tablero de mandos, compruebe que el dispositivo está enviando ahora 75 como el nuevo valor de temperatura.

## <a name="next-steps"></a>Próximos pasos

El artículo [Personalizar soluciones preconfiguradas] [ lnk-customize] describe algunas maneras en que puede extender este ejemplo. Posibles extensiones incluyen el uso de sensores reales y la implementación de comandos adicionales.

Puede obtener más información acerca de los [permisos en el sitio de azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
