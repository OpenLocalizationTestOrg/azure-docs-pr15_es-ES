<properties
    pageTitle="Usar telemetría dinámico | Microsoft Azure"
    description="Siga este curso para aprender a usar telemetría dinámico con la solución de preconfigurados supervisión."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Usar telemetría dinámico con la solución de preconfigurados supervisión

## <a name="introduction"></a>Introducción

Telemetría dinámica le permite visualizar cualquier telemetría enviado a la solución preconfigurada supervisión remota. Los dispositivos simulados que implementación con la solución preconfigurada envían telemetría temperatura y humedad, que se puede visualizar en el panel. Si personalizar los dispositivos simulados existentes, crear nuevos dispositivos simulados o conectar dispositivos físicos con la solución preconfigurada puede enviar otros valores de telemetría como la temperatura externa, RPM o velocidad del viento. A continuación, se puede visualizar este telemetría adicional en el panel.

Este tutorial usa un dispositivo simulado Node.js simple que puede modificar fácilmente para experimentar con telemetría dinámico.

Para completar este tutorial, necesitará:

- Una suscripción activa de Azure. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure][lnk_free_trial].
- [Node.js] [ lnk-node] versión 0.12.x o posterior.

Puede completar este tutorial en ningún sistema operativo, como Windows o Linux, donde puede instalar Node.js.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>Configurar el dispositivo simulado Node.js

1. En el panel control remoto, haga clic en **+ Agregar un dispositivo** y, a continuación, agregue un dispositivo personalizado. Anote el concentrador IoT hostname, el identificador de dispositivo y clave del dispositivo. Se necesita más adelante en este tutorial al preparar la aplicación de cliente de dispositivo remote_monitoring.js.

2. Asegúrese de que la versión Node.js 0.12.x o posterior está instalado en su equipo de desarrollo. Ejecutar `node --version` en el símbolo o en un shell para comprobar la versión. Para obtener información sobre cómo usar un administrador de paquete para instalar Node.js en Linux, vea [Instalar Node.js a través del Administrador de paquetes][node-linux].

3. Cuando se ha instalado Node.js, clonar la última versión de los [SDK de azure iot] [ lnk-github-repo] repositorio a su equipo de desarrollo. Usar siempre la rama **principal** para la versión más reciente de las bibliotecas y ejemplos.

4. Desde su copia local de los [SDK de azure iot] [ lnk-github-repo] repositorio, copiar las dos siguientes archivos de la carpeta de dispositivo de nodo/ejemplos vaciar la carpeta en el equipo de desarrollo:

  - Packages.JSON
  - remote_monitoring.js

5. Abra el archivo remote_monitoring.js y busque la definición de variable siguiente:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Reemplace **[cadena de conexión del dispositivo de concentrador IoT]** con la cadena de conexión del dispositivo. Use los valores para su concentrador IoT hostname, el identificador de dispositivo y la clave de dispositivo anotó en el paso 1. Una cadena de conexión de dispositivo tiene el siguiente formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Si el nombre de host de concentrador IoT es **contoso** y el identificador de dispositivo es **mydevice**, la cadena de conexión es similar a la siguiente:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Guarde el archivo. Ejecute los comandos siguientes en un shell o el símbolo del sistema en la carpeta que contiene estos archivos para instalar los paquetes necesarios y, a continuación, ejecute la aplicación de ejemplo:

    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observar dinámico telemetría en acción

El panel muestra la temperatura y humedad telemetría de los dispositivos simuladas existentes:

![El panel predeterminado][image1]

Si selecciona el dispositivo simulado Node.js que ejecutó en la sección anterior, verá la temperatura, humedad y telemetría temperatura externo:

![Agregar al escritorio de temperatura externo][image2]

La solución de supervisión automáticamente detecta el tipo de telemetría de temperatura externos adicionales y agrega al gráfico en el panel.

## <a name="add-a-telemetry-type"></a>Agregar un tipo de telemetría

El siguiente paso es reemplazar la telemetría generada por el dispositivo simulado Node.js con un nuevo conjunto de valores:

1. Detener el dispositivo simulado Node.js escribiendo **CTRL+c** en el símbolo o el shell.

2. En el archivo remote_monitoring.js, puede ver los valores de la base de datos para la temperatura existente, humedad y telemetría temperatura externa. Agregue un valor de base de datos para **rpm** como sigue:

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Dispositivo simulado Node.js usa la función **generateRandomIncrement** en el archivo remote_monitoring.js para agregar un incremento aleatorio los valores de la base de datos. El valor de **rpm** de forma aleatoria agregando una línea de código después de la randomizations existentes como sigue:

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Agregar el nuevo valor rpm a la carga JSON que el dispositivo envía a IoT concentrador:

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Ejecute el dispositivo simulado Node.js mediante el siguiente comando:

    ```
    node remote_monitoring.js
    ```

6. Observe el nuevo tipo de telemetría RPM que se muestra en el gráfico en el panel:

![Agregar al escritorio de RPM][image3]

> [AZURE.NOTE] Debe deshabilitar y, a continuación, habilite el dispositivo Node.js en la página de **dispositivos** en el panel para ver los cambios inmediatamente.

## <a name="customize-the-dashboard-display"></a>Personalizar la presentación de paneles

El mensaje de **Información del dispositivo** puede incluir metadatos sobre la telemetría que el dispositivo puede enviar a IoT concentrador. Estos metadatos pueden especificar los tipos de telemetría que envía el dispositivo. Modifique el valor de **deviceMetaData** en el archivo remote_monitoring.js para incluir una definición de **telemetría** siguiendo la definición de **comandos** . Fragmento de código siguiente muestra la definición de **comandos** (asegúrese de agregar una `,` después de la definición de **comandos** ):

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] La solución de supervisión usa a una coincidencia entre mayúsculas y minúsculas para comparar la definición de metadatos con datos en la secuencia de telemetría.

Agregar una definición de **telemetría** tal como se muestra en el fragmento de código anterior no cambia el comportamiento de los paneles. Sin embargo, los metadatos también pueden incluir un atributo **DisplayName** para personalizar la presentación en el panel. Actualizar la definición de metadatos de **telemetría** tal como se muestra en el fragmento de código siguiente:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

La siguiente captura de pantalla muestra cómo este cambio modifica la leyenda de gráfico en el panel:

![Personalizar la leyenda del gráfico][image4]

> [AZURE.NOTE] Debe deshabilitar y, a continuación, habilite el dispositivo Node.js en la página de **dispositivos** en el panel para ver los cambios inmediatamente.

## <a name="filter-the-telemetry-types"></a>Filtrar los tipos de telemetría

De forma predeterminada, el gráfico en el panel muestra cada serie de datos en la secuencia de telemetría. Puede usar los metadatos de **Información del dispositivo** para suprimir la presentación de tipos de telemetría específico en el gráfico. 

Para hacer que el gráfico mostrar solo de telemetría de temperatura y humedad, omitir **ExternalTemperature** de los metadatos de **telemetría** de **Información del dispositivo** :

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

La **Temperatura al aire libre** ya no se muestra en el gráfico:

![La telemetría en el panel de filtro][image5]

Este cambio sólo afecta a la presentación del gráfico. Los valores de datos de **ExternalTemperature** aún se almacenan y disponible para los procesos de back-end.

> [AZURE.NOTE] Debe deshabilitar y, a continuación, habilite el dispositivo Node.js en la página de **dispositivos** en el panel para ver los cambios inmediatamente.

## <a name="handle-errors"></a>Controlar los errores

Para que un flujo de datos mostrar en el gráfico, su **tipo** de los metadatos de **Información del dispositivo** debe coincidir con el tipo de datos de los valores de telemetría. Por ejemplo, si los metadatos que se especifican que el **tipo** de datos de humedad es **int** y **double** se encuentra en la secuencia de telemetría la telemetría humedad no mostrar en el gráfico. Sin embargo, los valores de **humedad** se almacena y disponible para los procesos de back-end.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha visto cómo usar telemetría dinámico, puede obtener más información sobre cómo las soluciones preconfiguradas utilizan información del dispositivo: [solución preconfigurada de supervisión de metadatos de información de dispositivo en el equipo remoto][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks
