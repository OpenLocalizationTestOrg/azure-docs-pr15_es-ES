<properties
    pageTitle="Personalizar soluciones preconfiguradas | Microsoft Azure"
    description="Proporciona instrucciones sobre cómo personalizar las soluciones de Azure IoT Suite preconfigurado."
    services=""
    suite="iot-suite"
    documentationCenter=".net"
    authors="aguilaaj"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/11/2016"
     ms.author="aguilaaj"/>

# <a name="customize-a-preconfigured-solution"></a>Personalizar una solución preconfigurada

Las soluciones preconfiguradas proporcionadas con el conjunto de Azure IoT muestran los servicios incluidos en el conjunto trabajar conjuntamente para ofrecer una solución de llevar a cabo. Desde este punto de partida, hay una gran variedad de lugares en los que puede ampliar y personalizar la solución para escenarios específicos. Las siguientes secciones describen estos puntos de personalización comunes.

## <a name="finding-the-source-code"></a>Buscar el código fuente

El código fuente de las soluciones preconfigurados está disponible en GitHub en los siguientes repositorios:

- Supervisión remota: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Mantenimiento predictivo: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

El código fuente de las soluciones preconfiguradas se proporciona para mostrar los patrones y las prácticas utilizadas para implementar la funcionalidad de llevar a cabo de una solución IoT con el conjunto de aplicaciones de Azure IoT. Puede encontrar más información sobre cómo crear e implementar las soluciones de los repositorios GitHub.

## <a name="changing-the-preconfigured-rules"></a>Cambiar las reglas preconfiguradas

La solución de supervisión incluye tres trabajos de [Análisis de secuencia de Azure](https://azure.microsoft.com/services/stream-analytics/) para implementar la lógica de información, telemetría y reglas de dispositivo muestran para la solución.

Los trabajos de análisis de secuencia de tres y su sintaxis se describe en profundidad en el [Tutorial de solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-sample-walkthrough.md). 

Puede editar estos trabajos directamente para modificar la lógica o agregar lógica específica para su situación. Puede encontrar los trabajos de análisis de secuencia como sigue:
 
1. Vaya al [portal de Azure](https://portal.azure.com).
2. Vaya al grupo de recursos con el mismo nombre que la solución IoT. 
3. Seleccione el trabajo de análisis de secuencia de Azure que desea modificar. 
4. Detener el trabajo, seleccione **Detener**en el conjunto de comandos. 
5. Editar las entradas, consulta y salidas.

    Una simple modificación consiste en cambiar la consulta para el trabajo de **reglas** usar un **"<"** en lugar de un **">"**. El portal de solución seguirá mostrando **">"** al editar una regla, pero observará el comportamiento se voltea debido a los cambios en el trabajo subyacente.

6. Iniciar el trabajo

> [AZURE.NOTE] El panel control remoto depende de datos específicos, alterar los trabajos puede causar el panel de errores.

## <a name="adding-your-own-rules"></a>Agregar sus propias reglas

Además de cambiar los trabajos de análisis de secuencia de Azure preconfigurados, puede usar el portal de Azure para agregar nuevas tareas o agregar nuevas consultas a trabajos existentes.

## <a name="customizing-devices"></a>Personalización de dispositivos

Una de las actividades más comunes de extensión está trabajando con dispositivos específicos para su situación. Existen varios métodos para trabajar con dispositivos. Estos métodos incluyen alterar un dispositivo simulado para que coincida con su situación o mediante el [SDK de dispositivo IoT][] conectar el dispositivo físico para la solución.

Para obtener una guía paso a paso para agregar dispositivos para la solución de preconfigurados supervisión, vea [Iot Suite conectar dispositivos](iot-suite-connecting-devices.md) y el [Ejemplo de SDK de C supervisión remota](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) que está diseñado para funcionar con la solución de preconfigurados supervisión.

### <a name="creating-your-own-simulated-device"></a>Creación de su propia simulación de dispositivo

Incluido en el código de origen de solución supervisión remota (mencionado anteriormente), es un simulador .NET. Este simulador es el se aprovisione como parte de la solución y se puede modificar para enviar metadatos diferentes, telemetría o responder a comandos diferentes.

El simulador preconfigurado en la solución de preconfigurados supervisión es un dispositivo refrigerador que emite telemetría temperatura y humedad, puede modificar el simulador en el proyecto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) cuando ha bifurcado el repositorio de GitHub.

### <a name="available-locations-for-simulated-devices"></a>Ubicaciones disponibles para dispositivos simulados

El valor predeterminado de ubicaciones está en Seattle o Redmond, Washington, Estados Unidos. Puede cambiar estas ubicaciones en [SampleDeviceFactory.cs][lnk-sample-device-factory].


### <a name="building-and-using-your-own-physical-device"></a>Crear y usar su propio dispositivo (físico)

El [SDK de Azure IoT](https://github.com/Azure/azure-iot-sdks) proporcionan bibliotecas para conectar varios tipos de dispositivo (idiomas y sistemas operativos) en IoT soluciones.

## <a name="modifying-dashboard-limits"></a>Modificar los límites de paneles

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Número de dispositivos que se muestran en la lista desplegable de paneles

El valor predeterminado es 200. Puede cambiar este número de [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Número de puntos de conexión para mostrar en el control de mapa de Bing

El valor predeterminado es 200. Puede cambiar este número de [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Período de tiempo de gráfico de telemetría

El valor predeterminado es de 10 minutos. Puede cambiar esta opción en [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configurar manualmente las funciones de aplicación

El siguiente procedimiento describe cómo agregar funciones de aplicación de **administración** como de **sólo lectura** a una solución preconfigurada. Observe que soluciones preconfiguradas aprovisionadas desde el sitio de azureiotsuite.com ya incluyen los roles de **Administrador** y **sólo lectura** .

Los miembros de la función de **sólo lectura** pueden ver el panel y la lista de dispositivos, pero no tiene permiso para agregar dispositivos, cambiar los atributos del dispositivo o enviar comandos.  Miembros de la función de **Administrador** tienen acceso completo a todas las funciones en la solución.

1. Vaya al [portal clásica Azure][lnk-classic-portal].

2. Seleccione **Active Directory**.

3. Haga clic en el nombre del inquilino AAD que utilizan cuando aprovisiona la solución.

4. Haga clic en **aplicaciones**.

5. Haga clic en el nombre de la aplicación que coincida con el nombre de la solución preconfigurada. Si no ve la aplicación en la lista, seleccione **aplicaciones es propietario de mi compañía** en **Mostrar** lista desplegable y haga clic en la marca de verificación.

6.  En la parte inferior de la página, haga clic en **Administrar manifiestos** y, a continuación, **Descargar manifiestos**.

7. Esto descarga un archivo de .json en el equipo local.  Abrir este archivo para editarlo en un editor de texto de su elección.

8. En la tercera línea del archivo .json, encontrará:

  ```
  "appRoles" : [],
  ```
  Reemplazar por lo siguiente:

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. Guarde el archivo actualizado .json (puede sobrescribir el archivo existente).

10.  En el Portal de administración de Azure en la parte inferior de la página, seleccione **Administrar manifiestos** **Manifiestos cargar** para cargar el archivo .json que guardó en el paso anterior.

11. Ahora ha agregado los roles de **Administrador** y de **sólo lectura** a la aplicación.

12. Para asignar una de estas funciones a un usuario en el directorio, consulte [permisos en el sitio de azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Comentarios

¿Tiene una personalización que le gustaría ver cubiertos en este documento? Agregue sugerencias característica [Voz de usuario](https://feedback.azure.com/forums/321918-azure-iot), o un comentario en este artículo. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de las opciones para personalizar las soluciones preconfiguradas, consulte:

- [Conectar lógica de aplicación a su solución de Azure IoT Suite la supervisión remota preconfigurados][lnk-logicapp]
- [Usar telemetría dinámico con la solución de preconfigurados supervisión][lnk-dynamic]
- [Metadatos de información de dispositivo en la solución preconfigurado supervisión remota][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[Dispositivo IoT SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
