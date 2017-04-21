<properties
 pageTitle="Crear una aplicación de la función Azure y la cuenta de almacenamiento de Azure | Microsoft Azure"
 description="La aplicación de la función Azure escucha eventos de concentrador de Azure IoT, procesa los mensajes entrantes y escribe en el almacenamiento de tablas de Azure."
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

# <a name="31-create-an-azure-function-app-and-azure-storage-account"></a>3.1 crear una aplicación de la función Azure y la cuenta de almacenamiento de Azure

[Funciones de Azure](../../articles/azure-functions/functions-overview.md) es una solución para ejecutar fácilmente pequeños fragmentos de código, denominada "acciones", en la nube. Una aplicación de la función Azure aloja la ejecución de las funciones de Azure.

## <a name="311-what-will-you-do"></a>3.1.1 ¿qué hará

Usar una plantilla de administrador de recursos de Azure para crear una aplicación de la función Azure y una cuenta de almacenamiento de Azure. La aplicación de la función Azure escucha eventos de concentrador de Azure IoT, procesa los mensajes entrantes y escribe en el almacenamiento de tablas de Azure. Si se cumple algún problema, buscar soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="312-what-will-you-learn"></a>3.1.2 lo que aprenderá

- Cómo usar el [Administrador de recursos de Azure](../../articles/azure-resource-manager/resource-group-overview.md) para implementar recursos Azure.
- Cómo usar una aplicación de la función Azure para procesar los mensajes de concentrador IoT y escribirlos en una tabla en el almacenamiento de tablas de Azure.

## <a name="313-what-do-you-need"></a>3.1.3 lo que necesita

- Debe finalizó correctamente las lecciones anteriores: [crear su centro de Azure IoT](iot-hub-raspberry-pi-kit-node-get-started.md)y [empezar a trabajar con su frambuesas Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md) .

## <a name="314-open-the-sample-app"></a>3.1.4 abre la aplicación de ejemplo

Abra el proyecto de ejemplo en el código de Visual Studio, ejecute los comandos siguientes:

```bash
cd Lesson3
code .
```

![Estructura de repo](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

- La `app.js` de archivo de la `app` subcarpeta es el archivo de origen de la clave. Este archivo de origen contiene el código para enviar un mensaje 20 horas a su centro IoT y parpadea el LED para cada mensaje que se envía.
- La `arm-template.json` archivo es la plantilla de administrador de recursos de Azure que contiene una aplicación de la función Azure y una cuenta de almacenamiento de Azure.
- La `arm-template-param.json` es el archivo de configuración usado por la plantilla de administrador de recursos de Azure.
- La `ReceiveDeviceMessages` subcarpeta contiene el código de Node.js de la función de Azure.

## <a name="315-configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>3.1.5 configurar plantillas de Azure Administrador de recursos y crear recursos en Azure

Actualizar la `arm-template-param.json` archivo en código de Visual Studio.

![Parámetros de plantilla de Azure Administrador de recursos](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

- Reemplace **[su nombre de concentrador IoT]** con **{Mi nombre de concentrador}** especificada en la [lección 2](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).
- Reemplace **[cadena de prefijo para los nuevos recursos]** con cualquier prefijo que desee. El prefijo garantiza que el nombre del recurso es único global para evitar conflictos. No use guión o el número inicial en el prefijo.

> [AZURE.NOTE] No es necesario `azure_storage_connection_string` en esta sección. Mantener tal como está.

Después de actualizar la `arm-template-param.json` de archivos, implementar los recursos en Azure, ejecute el siguiente comando:

```bash
az resource group deployment create --template-file-path arm-template.json --parameters-file-path arm-template-param.json -g iot-sample -n mydeployment
```

Tardará unos cinco minutos para crear estos recursos. Mientras se realiza la creación de recursos, puede mover a la siguiente sección.

## <a name="316-summary"></a>3.1.6 resumen de

Ha creado la aplicación de la función Azure para procesar IoT concentrador mensajes de una cuenta de almacenamiento de Azure para almacenar estos mensajes. Puede mover a la siguiente sección para implementar y ejecutar el ejemplo para enviar mensajes de la nube de dispositivo en el Pi.

## <a name="next-steps"></a>Pasos siguientes

[3.2 ejecutar la aplicación de ejemplo para enviar mensajes de nube de dispositivo en el Pi frambuesas 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

