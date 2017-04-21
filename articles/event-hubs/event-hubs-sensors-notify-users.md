<properties 
   pageTitle="Notificar a los usuarios de datos recibidos de sensores u otros sistemas | Microsoft Azure"
   description="Describe cómo utilizar Hubs de evento para notificar a los usuarios de datos de sensor."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="notify-users-of-data-received-from-sensors-or-other-systems"></a>Notificar a los usuarios de datos recibidos de sensores u otros sistemas

Suponga que tiene una aplicación que supervisa datos en tiempo real o genera informes en una programación. Si mira el sitio Web en el que se muestran los gráficos en tiempo real o informes, es posible que vea algo que requiere una acción. ¿Qué sucede si necesita ser avisa a las situaciones, en lugar de depender de recordar comprobar el sitio Web? Suponga que tiene una luz aumentar en un efecto de invernadero y que debe saber inmediatamente si la bombilla. Una forma de hacerlo sería con un sensor de luz en invernadero, organizar se envíe un correo electrónico si la luz está desactivada.

![][1]

En otro escenario, suponga que ejecute una función de acceso para animales y debe recibir una alerta para niveles de suministro de inventario bajo. Por ejemplo, puede organizar enviarse un mensaje de texto desde el sistema ERP si el inventario de almacén de comida perro descienda al nivel crítico. 

![][2]

El problema es cómo obtener información esencial cuando se cumplan ciertas condiciones, no al moverse a desproteger un informe estático. Si está utilizando un [Concentrador de evento de Azure][] o [Azure IoT concentrador][] para recibir datos desde dispositivos o aplicaciones empresariales como [Dynamics AX][], tiene varias opciones para cómo se procesan. Puede ver en un sitio Web, puede analizarlos, puede almacenarlos y usarlos para desencadenar comandos que hacer algo. Para ello, puede usar herramientas eficaces como [Sitios Web de Azure][], [SQL Azure][], [HDInsight][], [Conjunto de aplicaciones de inteligencia de Cortana][], [IoT Suite][], [Lógica de aplicaciones][]o [Hubs de notificación de Azure][]. Pero a veces todo lo que desee hacer es enviar datos a alguien con un mínimo de sobrecarga. Para mostrar cómo hacerlo con un poco de código, hemos incluido una muestra nueva, [AppToNotifyUsers][]. Opciones incluidas son correo (SMTP), SMS y teléfono.

## <a name="application-structure"></a>Estructura de la aplicación

La aplicación está escrita en C# y el archivo Léame de los datos de ejemplo contiene toda la información que necesita modificar, crear y publicar la aplicación. Las siguientes secciones proporcionan una descripción general de las funciones de la aplicación.

Empezaremos con la suposición de que tiene los eventos más importantes que se envían a un concentrador de evento de Azure o IoT concentrador. Cualquier concentrador hará, siempre y cuando tenga acceso a él y conocer la cadena de conexión.

Si no dispone de un concentrador de evento o IoT concentrador, puede configurar fácilmente un jardín de prueba con una pantalla Arduino y un Pi frambuesas, siguiendo las instrucciones del proyecto [Conecte los puntos](https://github.com/Azure/connectthedots) . El sensor de luz en la pantalla Arduino envía los niveles de luz a través de la Pi a un [Concentrador de evento de Azure][] (**ehdevices**) y, a continuación, una tarea de [Análisis de secuencia de Azure](https://azure.microsoft.com/services/stream-analytics/) inserta alertas a un concentrador de evento segundo (**ehalerts**) si los niveles de luz recibidos se encuentran por debajo de un determinado nivel.

Cuando se inicia **AppToNotify** , lee un archivo de configuración (App.config) para obtener la dirección URL y las credenciales para el concentrador de evento recibir las alertas. A continuación, que genera un proceso para supervisar continuamente que concentrador de evento para todos los mensajes que se entrega a través de – siempre y cuando tenga que puede tener acceso a la dirección URL para el evento concentrador o IoT concentrador y credenciales válidas, este código del lector del evento Hubs continuamente leerá Novedades de. Durante el inicio, la aplicación lee también la dirección URL y las credenciales para el servicio de mensajería (teléfono de correo electrónico, SMS,) que desea usar y el nombre o la dirección del remitente y una lista de destinatarios.

Una vez que el monitor de eventos concentrador detecta un mensaje, activa un proceso que envía el mensaje utilizando el método especificado en el archivo de configuración. Tenga en cuenta que envía todos los mensajes detecta. Si establece el monitor para que apunten a un concentrador de eventos que recibe los diez mensajes por segundo, el remitente le enviará diez mensajes por segundo: diez mensajes de correo electrónico por segundo, mensajes de SMS de diez por segundo, llamadas de teléfono diez por segundo. Por este motivo, asegúrese de que supervisar un concentrador de evento que sólo recibe las alertas que deben enviarse, no un concentrador de eventos que recibe todos los datos sin formato de las aplicaciones o sensores.

## <a name="applicability"></a>Aplicación

El código de este ejemplo solo muestra cómo supervisar el evento Hubs y cómo llamar a los servicios de mensajería externos en caso de que desea agregar esta funcionalidad a la aplicación. Tenga en cuenta que esta solución es una y MANUALIDADES, centrados programador ejemplo solo. No trata los requisitos empresariales como redundancia de fallos, reinicie tras error, etcetera. Para obtener más soluciones integrales y de producción, consulte lo siguiente:

- Usando los conectores o las notificaciones de inserción con el servicio de [Aplicaciones de lógica de Azure](../app-service-logic/app-service-logic-connectors-list.md) .
- Usando [Hubs de notificación de Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx), como se describe el blog de [notificaciones de inserción de difusión a millones de dispositivos móviles con Hubs de notificación de Azure](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs). 

## <a name="next-steps"></a>Pasos siguientes

Es sencillo crear un servicio de notificación simple que envía mensajes de correo electrónico o mensajes de texto a los destinatarios o ellos, llama a los datos de retransmisión recibidos por un evento concentrador o IoT concentrador. Para implementar la solución para notificar a los usuarios a partir de datos que recibe estos hubs, visite [AppToNotifyUsers][].

Para obtener más información sobre estas hubs, consulte los siguientes artículos:

- [Evento Azure Hubs]
- [Hub de Azure IoT]
- Introducción a un [evento Hubs tutorial].
- Una [aplicación de ejemplo que usa el evento Hubs]completa.

Para implementar la solución para notificar a los usuarios basados en datos recibidos por estos hubs, visite:

- [AppToNotifyUsers][]

[Tutorial de Hubs de evento]: event-hubs-csharp-ephcs-getstarted.md
[Hub de Azure IoT]: https://azure.microsoft.com/services/iot-hub/
[Evento Azure Hubs]: https://azure.microsoft.com/services/event-hubs/
[Hub de evento de Azure]: https://azure.microsoft.com/services/event-hubs/
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Sitios Web de Azure]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana inteligencia Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[Aplicaciones de lógica]: https://azure.microsoft.com/services/app-service/logic/
[Notificación de Azure Hubs]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png