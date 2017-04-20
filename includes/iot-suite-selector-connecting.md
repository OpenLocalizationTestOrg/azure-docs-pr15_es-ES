> [AZURE.SELECTOR]
- [C en Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C en Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C en mbed para](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## <a name="scenario-overview"></a>Resumen de escenario

En este escenario, se crea un dispositivo que envía la telemetría siguiente a la [solución preconfigurada]de control remoto[lnk-what-are-preconfig-solutions]:

- Temperatura externa
- Temperatura interna
- Humedad

Por simplicidad, el código en el dispositivo genera valores de ejemplo, pero le animamos a ampliar el ejemplo conectando sensores reales a su dispositivo y enviar telemetría real.

Para completar este tutorial, necesita una cuenta activa de Azure. Si no tienes una cuenta, puede crear una cuenta de prueba gratuita en sólo un par de minutos. Para obtener más información, vea [Versión de prueba gratuita de Azure][lnk-free-trial].

## <a name="before-you-start"></a>Antes de empezar

Antes de escribir ningún código para su dispositivo, debe aprovisionar la solución preconfigurada de supervisión remota y luego suministrar un dispositivo personalizado nuevo en esa solución.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Aprovisionar la solución preconfigurada de supervisión remota

El dispositivo se crea en este tutorial envía datos a una instancia de la [supervisión remota] de[ lnk-remote-monitoring] preconfigurado la solución. Si ya no ha suministrado la solución preconfigurada de supervisión remota en su cuenta de Azure, siga los siguientes pasos:

1. En la página <https://www.azureiotsuite.com/> , haga clic en **+** para crear una nueva solución.

2. Haga clic en **Seleccionar** en el panel de **supervisión remota** para crear su nueva solución.

3. En la página **Crear solución de supervisión** , escriba un **nombre de la solución** de su elección, seleccione el **área** que desea implementar en y seleccione la suscripción de Azure que desea utilizar. A continuación, haga clic en **Crear solución**.

4. Espere a que finalice el proceso de aprovisionamiento.

> [AZURE.WARNING] Las soluciones preconfiguradas utilizan servicios de Azure facturables. Asegúrese de quitar la solución preconfigurada de la suscripción cuando haya terminado con él para evitar los gastos innecesarios. Puede quitar completamente una solución preconfigurada en su suscripción visitando la página <https://www.azureiotsuite.com/> .

Cuando finalice el proceso de aprovisionamiento para la solución de monitoreo remoto, haga clic en **Inicio** para abrir el panel de la solución en el explorador.

![][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Configurar el dispositivo en la solución de supervisión remota

> [AZURE.NOTE] Si ya ha suministrado un dispositivo en su solución, puede omitir este paso. Necesitará conocer las credenciales del dispositivo al crear la aplicación cliente.

Para que un dispositivo para conectarse a la solución preconfigurada, debe identificar ante el concentrador IoT con credenciales válidas. Puede recuperar las credenciales del dispositivo desde el panel de la solución. En la aplicación de cliente más adelante en este tutorial, debe incluir las credenciales del dispositivo. 

Para agregar un dispositivo nuevo a la solución de supervisión remota, siga los pasos siguientes en el panel de la solución:

1.  En la esquina inferior izquierda del panel, haga clic en **Agregar un dispositivo**.

    ![][1]

2.  En el panel de **Dispositivo personalizado** , haga clic en **Agregar nuevo**.

    ![][2]

3.  Elija **Permitirme definir mi propio ID de dispositivo**, especifique un ID de dispositivo, como **mydevice**, haga clic en **Check ID** para comprobar que ese nombre no está en uso y, a continuación, haga clic en **crear** para poner en servicio el dispositivo.

    ![][3]

5. Tome nota de las credenciales del dispositivo (Id. de dispositivo IoT concentrador Hostname y clave de dispositivo), la aplicación cliente necesita para conectarse a la solución de supervisión remota. A continuación, haga clic en **Listo**.

    ![][4]

6. Asegúrese de que el dispositivo se muestra en la sección dispositivos. El estado del dispositivo está **pendiente** hasta que el dispositivo establece una conexión con la solución de supervisión remota.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/