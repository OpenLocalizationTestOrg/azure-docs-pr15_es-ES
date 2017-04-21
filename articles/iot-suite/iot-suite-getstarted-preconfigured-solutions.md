<properties
    pageTitle="Introducción a soluciones preconfiguradas | Microsoft Azure"
    description="Siga este curso para aprender a implementar una solución de Azure IoT Suite preconfigurado."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Tutorial: Introducción a las soluciones preconfiguradas

## <a name="introduction"></a>Introducción

Azure IoT Suite [soluciones preconfiguradas] [ lnk-preconfigured-solutions] combinar varios servicios de Azure IoT para ofrecer soluciones to-end que implementan escenarios empresariales de IoT comunes. La solución de *supervisión remota* preconfiguradas se conecta a y supervisa los dispositivos. Puede usar la solución para analizar el flujo de datos desde sus dispositivos y para mejorar los resultados de negocio realizando procesos responder automáticamente a esa secuencia de datos.

Este tutorial muestra cómo aprovisionar la solución preconfigurada supervisión remota. También le guía a través de las características básicas de la solución de supervisión. Puede acceder a muchas de estas características mediante el panel de la solución que implementa junto con la solución preconfigurada:

![Panel de solución preconfigurada supervisión remota][img-dashboard]

Para completar este tutorial, necesita una suscripción activa de Azure.

> [AZURE.NOTE]  Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Ver el panel de la solución

El panel de la solución permite administrar la solución implementada. Por ejemplo, puede ver telemetría, agregar dispositivos y configurar las reglas.

1.  Cuando el aprovisionamiento se completa y el mosaico de la solución preconfigurado indica **Listo**, haga clic en **Inicio** para abrir el portal de solución de supervisión remoto en una nueva pestaña.

    ![Iniciar la solución preconfigurada][img-launch-solution]

2.  De forma predeterminada, el portal de solución muestra el *panel de la solución*. Puede seleccionar otras vistas mediante el menú de la izquierda.

    ![Panel de solución preconfigurada supervisión remota][img-dashboard]

El panel muestra la siguiente información:

- El mapa muestra la ubicación de cada dispositivo conectado a la solución. Cuando ejecute la solución por primera vez, hay cuatro dispositivos simulados. Los dispositivos simulados se implementan como WebJobs de Azure y la solución utiliza la API de mapas de Bing para trazar la información en el mapa.
- El panel de **Telemetría historial** traza telemetría humedad y temperatura desde un dispositivo en casi en tiempo real y muestra los datos agregado como humedad máximo, mínimo y promedio.
- El panel **Historial de alarma** muestra recientes eventos de alarma cuando un valor de telemetría ha superado un umbral. Puede definir sus propios alarmas además de los ejemplos creados por la solución preconfigurada.

## <a name="view-the-device-list"></a>Ver la lista de dispositivos

La lista de dispositivos muestra todos los dispositivos registrados en la solución. Ver y editar los metadatos de dispositivo, agregar o quitar dispositivos y enviar comandos a los dispositivos.

1.  En el menú de la izquierda para mostrar la *lista de dispositivos* para esta solución, haga clic en **dispositivos** .

    ![Lista de dispositivos en los paneles][img-devicelist]

2.  La lista de dispositivos muestra que hay cuatro dispositivos simulados creados por el proceso de aprovisionamiento.

3.  Haga clic en un dispositivo en la lista de dispositivos para ver los detalles del dispositivo.

    ![En el panel de detalles del dispositivo][img-devicedetails]

El panel de **Detalles del dispositivo** contiene tres secciones:

- La sección **acciones** muestra las acciones que puede realizar en el dispositivo. Si deshabilita el dispositivo, ya no se pueden enviar telemetría o recibir comandos. Si deshabilita un dispositivo, a continuación, puede habilitar nuevamente. Puede agregar una regla asociada con el dispositivo que desencadene una alarma cuando un valor de telemetría supera un umbral. También puede enviar un comando a un dispositivo. Cuando un dispositivo se conecta en primer lugar, indica que la solución los comandos que puede responder a.
- La sección de **Propiedades de dispositivos** enumera los metadatos del dispositivo. Algunos de estos metadatos proceden del dispositivo (por ejemplo, el fabricante) y algunas se genera la solución (por ejemplo, la hora de creación). Puede editar los metadatos de dispositivo desde aquí.
- La sección de **Claves de autenticación** enumera las teclas que puede usar el dispositivo para autenticar con la solución.

## <a name="send-a-command-to-a-device"></a>Enviar un comando a un dispositivo

El panel de detalles del dispositivo muestra todos los comandos que admite un dispositivo específico y permite enviar comandos a un dispositivo. Cuando se inicie un dispositivo, envía información acerca de los comandos que admite la solución.

1.  Haga clic en **comandos** en el panel de detalles del dispositivo para el dispositivo seleccionado.

    ![Comandos de dispositivo de paneles][img-devicecommands]

2.  Seleccione **PingDevice** de la lista de comandos.

3.  Haga clic en el **comando Enviar**.

4.  Puede ver el estado del comando en el historial de comandos.

    ![Estado del comando en el panel][img-pingcommand]

La solución hace un seguimiento del estado de cada comando envía. Inicialmente, el resultado es **pendiente**. Cuando el dispositivo informa de que se ha ejecutado el comando, el resultado se establece como **correcto**.

## <a name="add-a-new-simulated-device"></a>Agregar un nuevo dispositivo simulado

Cuando se implementa la solución preconfigurada, automáticamente aprovisionar los cuatro dispositivos de muestra que puede ver en la lista de dispositivos. Estos dispositivos son *simulados dispositivos* que ejecutan en un WebJob de Azure. Dispositivos simulados facilitan experimentar con la solución preconfigurada sin necesidad de implementar dispositivos físicos reales. Si desea conectar un dispositivo real para la solución, consulte la [solución preconfigurada de supervisión de conectar el dispositivo en el equipo remoto] [ lnk-connect-rm] tutorial.

Los pasos siguientes muestran cómo agregar un dispositivo simulado a la solución:

1.  Vaya a la lista de dispositivos.

2.  Haga clic en **+ Agregar un dispositivo** en la esquina inferior izquierda para agregar un dispositivo.

    ![Agregar un dispositivo a la solución preconfigurada][img-adddevice]

3.  Haga clic en **Agregar nuevo** en el mosaico del **Dispositivo simulado** .

    ![Establecer detalles del dispositivo nuevo de paneles][img-addnew]
    
    Además de crear un nuevo dispositivo simulado, también puede agregar un dispositivo físico si elige crear un **Dispositivo personalizado**. Para obtener más información sobre cómo conectar dispositivos físicos a la solución, vea [conectar el dispositivo para el conjunto de IoT supervisión solución preconfigurada remota][lnk-connect-rm].

4.  Seleccione **Permitirme definir la mía ID de dispositivo**y escriba un nombre de identificador único del dispositivo, como **mydevice_01**.

5.  Haga clic en **crear**.

    ![Guardar un nuevo dispositivo][img-definedevice]

6. En el paso 3 de **Agregar un dispositivo simulado**, haga clic en **Listo** para volver a la lista de dispositivos.

7. Puede ver el dispositivo que se **ejecuta** en la lista de dispositivos.

    ![Ver nuevo dispositivo en la lista de dispositivos][img-runningnew]

8. También puede ver la telemetría simulada desde el dispositivo nuevo en el panel:

    ![Vista de telemetría de nuevo dispositivo][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Editar los metadatos de dispositivo

Cuando un dispositivo se conecta primero a la solución, envía sus metadatos a la solución. Al editar los metadatos de dispositivo mediante el panel de la solución, envía los nuevos valores de metadatos en el dispositivo y almacena los valores nuevos en la base de datos de solución DocumentDB. Para obtener más información, vea [registro de identidad de dispositivos y DocumentDB][lnk-devicemetadata].

1.  Vaya a la lista de dispositivos.

2.  Seleccione el nuevo dispositivo en la **Lista de dispositivos**y, a continuación, haga clic en **Editar** para editar las **Propiedades de dispositivos**:

    ![Editar los metadatos de dispositivo][img-editdevice]

3. Desplácese hacia abajo y realice un cambio en vales de latitud y longitud. A continuación, haga clic en **Guardar cambios en el registro del dispositivo**.

    ![Editar los metadatos de dispositivo][img-editdevice2]

4. Volver al panel, la ubicación del dispositivo ha cambiado en el mapa:

    ![Editar los metadatos de dispositivo][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Agregar una regla para el nuevo dispositivo

No hay ninguna regla para el nuevo dispositivo que acaba de agregar. En esta sección, agregar una regla que se activa una alarma cuando la temperatura notificada por el nuevo dispositivo supera 47 grados. Antes de empezar, observe que el historial de telemetría para el nuevo dispositivo en el panel muestra que la temperatura dispositivo nunca supera 45 grados.

1.  Vaya a la lista de dispositivos.

2.  Seleccione el nuevo dispositivo en la **Lista de dispositivos**y, a continuación, haga clic en **Agregar regla** para agregar una regla para el dispositivo.

3. Crear una regla que utiliza **temperatura** como el campo de datos y usa **AlarmTemp** como salida cuando la temperatura supera 47 grados:

    ![Agregar una regla de dispositivo][img-adddevicerule]

4. Haga clic en **Guardar y ver reglas** para guardar los cambios.

5.  Haga clic en **comandos** en el panel de detalles del dispositivo para el nuevo dispositivo.

    ![Agregar una regla de dispositivo][img-adddevicerule2]

6.  Seleccione **ChangeSetPointTemp** desde la lista de comandos y establezca **SetPointTemp** en 45. A continuación, haga clic en el **Comando Enviar**:

    ![Agregar una regla de dispositivo][img-adddevicerule3]

7.  Vaya al panel de la solución. Después de un rato, verá una nueva entrada en el panel de **Historial de alarma** cuando la temperatura notificada por el nuevo dispositivo supera el umbral de 47 grados:

    ![Agregar una regla de dispositivo][img-adddevicerule4]

8. Puede revisar y editar todas las reglas en la página **reglas** del panel:

    ![Reglas de dispositivos de lista][img-rules]

9. Puede revisar y editar todas las acciones que se pueden adoptar en respuesta a una regla en la página **acciones** del panel:

    ![Acciones de dispositivo de lista][img-actions]

> [AZURE.NOTE] Es posible definir las acciones que pueden enviar un mensaje de correo electrónico o SMS en respuesta a una regla o integrar con un sistema de línea de negocio a través de una [Aplicación de lógica][lnk-logic-apps]. Para obtener más información, vea el [Conectar lógica de aplicación para el control remoto de Azure IoT Suite preconfigurado solución][lnk-logicapptutorial].

## <a name="other-features"></a>Otras características

Con el portal de solución que se puede buscar para dispositivos con características específicas, como un número de modelo:

![Búsqueda de un dispositivo][img-search]

Puede deshabilitar un dispositivo y después está deshabilitado puede quitarlo:

![Deshabilitar y quitar un dispositivo][img-disable]

## <a name="behind-the-scenes"></a>Segundo plano

Cuando se implementa una solución preconfigurada, el proceso de implementación crea varios recursos en la suscripción de Azure seleccionado. Puede ver estos recursos en el [portal]de Azure[lnk-portal]. El proceso de implementación crea un **grupo de recursos** con un nombre basado en el nombre que elija para la solución preconfigurada:

![Solución preconfigurada en el portal de Azure][img-portal]

Puede ver la configuración de cada recurso, selecciónelo en la lista de recursos en el grupo de recursos.

También puede ver el código fuente de la solución preconfigurada. El código de origen de solución preconfigurada de supervisión remota está en la [supervisión una remota de iot azure] [ lnk-rmgithub] GitHub repositorio:

- La carpeta **DeviceAdministration** contiene el código fuente para el escritorio.
- La carpeta **Simulator** contiene el código fuente de dispositivo simulado.
- La carpeta **EventProcessor** contiene el código fuente para el proceso de back-end que controla la telemetría entrante.

Cuando haya terminado, puede eliminar la solución preconfigurada de su suscripción de Azure en la [azureiotsuite.com] [ lnk-azureiotsuite] sitio. Este sitio le permite eliminar fácilmente todos los recursos que se prepararon cuando creó la solución preconfigurada.

> [AZURE.NOTE] Para asegurarse de que elimina todos los elementos relacionados con la solución preconfigurada, eliminar en la [azureiotsuite.com] [ lnk-azureiotsuite] del sitio y no elimine el grupo de recursos en el portal.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado una solución de trabajo preconfigurada, puede seguir Introducción a Suite IoT lea los siguientes artículos:

- [Tutorial de solución preconfigurada supervisión remota][lnk-rm-walkthrough]
- [Conectar el dispositivo a la solución preconfigurada supervisión remota][lnk-connect-rm]
- [Permisos en el sitio de azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
