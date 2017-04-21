<properties
 pageTitle="Solución de problemas | Microsoft Azure"
 description="Solución de problemas de la página para obtener una experiencia de frambuesas Pi Node.js"
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="troubleshooting"></a>Solución de problemas

## <a name="hardware-issues"></a>Problemas de hardware

### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>La aplicación se ejecuta correctamente pero el LED no parpadea

Este problema siempre está relacionado con la conectividad de circuito de hardware. Siga los pasos siguientes para identificar los problemas.

1. Compruebe si elige la correcta **GPIO** en su panel. Los dos puertos en esta lección debería **GPIO GND (6 Pin)** y **GPIO 04 (Pin 7)**.
2. Compruebe si la polaridad de su LED es correcta. Debe indicar el segmento más **positivo**, pin ánodo.
3. Use el **3.3V anclar** y el **Pin GND** en el Pi frambuesas 3. Considerar el Pi como la corriente. Compruebe si el LED funciona correctamente.

![Especificación de LED](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Otros problemas de hardware

Para obtener información sobre cómo resolver problemas comunes en las 3 de Pi frambuesas, consulte la [página oficial de solución de problemas](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Problemas de paquete Node.js

### <a name="no-response-during-gulp-tasks"></a>Ninguna respuesta durante las tareas solo golpe

Si encuentra problemas al ejecutar solo golpe tareas, puede agregar la `--verbose` opción de depuración. Intenta terminar tareas solo golpe actuales con `Ctrl + C` y a continuación, ejecute el siguiente comando en la ventana de la consola para ver mensajes de depuración. Es posible que vea mensajes de error detallados impresos en el resultado de la consola. 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemas de detección de dispositivos

Para obtener ayuda de solución de problemas comunes con la `devdisco` comando, compruebe el [archivo Léame](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="other-npm-issues"></a>Otros problemas NPM

Intente actualizar el paquete NPM con el siguiente comando:

```bash
npm install -g npm
```

Si el problema persiste, dejar sus comentarios al final de este artículo o crear un problema de Github en nuestro [Ejemplo repositorio](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)

## <a name="remote-debugging"></a>Depuración remota

### <a name="run-the-sample-application-in-debug-mode"></a>Ejecute la aplicación de ejemplo en modo de depuración

```bash
gulp run --debug
```

Cuando el motor de depuración esté listo, debería ver ```Debugger listening on port 5858``` en el resultado de la consola.

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>Configurar el código de VS para conectar con el dispositivo remoto

Abra el panel de **depuración** en el lado izquierdo.

Haga clic en el botón **Iniciar depuración** (F5) verde. Código de VS abre un archivo **launch.json** , que es necesario actualizar.

Actualizar el archivo **launch.json** con el siguiente contenido. Reemplazar `[device hostname or IP address]` con la dirección IP del dispositivo real o el nombre de host.   

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![Configuración de depuración remota](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Adjuntar a la aplicación remota

Haga clic en el botón **Iniciar depuración** (F5) verde para iniciar la depuración. 

Lea [JavaScript en VS código](https://code.visualstudio.com/docs/languages/javascript#_debugging) para obtener más información sobre el depurador.

![Depuración interactiva remota](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Problemas CLI Azure

Azure CLI es una vista previa. Puede hacer referencia a la [Guía de instalación de vista previa](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md) para buscar soluciones.

Si encuentra problemas con la herramienta, archivo un [problema](https://github.com/Azure/azure-cli/issues) en la sección de **problemas** de la repo GitHub.

Para obtener ayuda para solucionar los problemas más comunes, consulte el [archivo Léame](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Problemas de instalación de Python

### <a name="legacy-installation-issues-macos"></a>Problemas de instalación heredados (Mac OS)

Al instalar **puntos**, se produce un error de permiso cuando hay paquetes heredados que se instalan con permisos de **su** . Esto ocurre porque no se desinstala completamente la instalación anterior de Python usando cerveza (Mac OS). Algunos paquetes de **puntos** de una instalación anterior se crearon raíz, que genera el error de permisos. La solución es quitar los paquetes instalados por raíz. Siga los pasos siguientes para completar esta tarea:

1. Vaya a /usr/local/lib/python2.7/site-packages
2. Paquetes de la lista crear raíz:`ls -l | grep root`
3. Desinstalar paquetes en paso 2:`sudo rm -rf {package name}`
4. Vuelva a instalar Python.

## <a name="azure-iot-hub-issues"></a>Problemas de concentrador de Azure IoT

Si ha suministrado correctamente concentrador de Azure IoT con `azure-cli`, y necesita una herramienta para administrar los dispositivos que se conecten a su centro IoT, pruebe las siguientes herramientas:

### <a name="device-explorer"></a>Explorador de dispositivo

[Explorador de dispositivo](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) se ejecuta en el equipo local de Windows y se conecta a su centro IoT en Azure. Se comunica con los siguientes [puntos finales de concentrador IoT](iot-hub-devguide.md):

- *Administración de identidades de dispositivo* para aprovisionar y administrar dispositivos registrado con su centro IoT.
- *Recepción dispositivo a la nube* para que pueda supervisar mensajes enviados desde el dispositivo a su centro IoT.
- *Enviar el dispositivo de nube* para que pueda enviar mensajes a los dispositivos de su centro IoT.

Configurar su `IoT hub connection string` dentro de esta herramienta para usar todas sus funciones.

### <a name="iot-hub-explorer"></a>Concentrador IoT Explorer

[Concentrador IoT Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) es una herramienta CLI varias plataformas de ejemplo para administrar a dispositivos clientes. La herramienta le permite administrar los dispositivos en el registro de identidad, supervisar los mensajes del dispositivo a la nube y enviar comandos de nube al dispositivo.

Para instalar la última versión (versión preliminar) de la herramienta iothub explorer, ejecute el comando siguiente en su entorno de línea de comandos:

```
npm install -g iothub-explorer@latest
```

Puede usar el comando siguiente para obtener ayuda adicional sobre todos los comandos del explorador de iothub y sus parámetros:

```bash
iothub-explorer help
```

### <a name="use-azure-portal-to-manage-your-resources"></a>Usar el portal de Azure para administrar los recursos

En todas estas lecciones, se ofrece una experiencia completa de CLI para crear y administrar los recursos de Azure. También puede usar el [portal de Azure](../azure-portal-overview.md) ayuda aprovisionar, administrar y depurar los recursos de Azure.

## <a name="azure-storage-issues"></a>Problemas de almacenamiento de Azure

[Explorador de Microsoft Azure almacenamiento (vista preliminar)](http://storageexplorer.com) es una aplicación independiente de Microsoft que le permite trabajar con datos de almacenamiento de Azure en Windows, Mac OS y Linux. Esta herramienta le permite conectarse a la tabla y ver los datos en él. Puede usar esta herramienta para solucionar los problemas de almacenamiento de Azure.
