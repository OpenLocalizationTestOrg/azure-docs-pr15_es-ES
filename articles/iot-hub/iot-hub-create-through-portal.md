<properties
     pageTitle="Usar el portal de Azure para crear un concentrador IoT | Microsoft Azure"
     description="Información general sobre cómo crear y administrar hubs IoT Azure a través del portal de Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-the-azure-portal"></a>Crear un concentrador IoT con el portal de Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]


## <a name="introduction"></a>Introducción

Este artículo describe cómo encontrar el servicio de concentrador IoT en el portal de Azure y cómo crear y administrar IoT hubs.

## <a name="where-to-find-iot-hubs"></a>Dónde encontrar IoT hubs

Existen varios lugares donde puede encontrar IoT hubs.

1. **+ Nuevo**: **Azure IoT concentrador** es un servicio de IoT y puede encontrarse en la categoría **Internet de cosas**, en **+ nuevo**, similar a otros servicios.

2. IoT hubs también pueden tener acceso mediante el catálogo de soluciones, como el servicio de imagen en **Internet de las cosas**.

## <a name="create-an-iot-hub"></a>Crear un concentrador IoT

Puede crear un concentrador IoT usando los métodos siguientes:

- Creación de un concentrador IoT mediante la opción **+ nuevo** lleva a la hoja que se muestra en la siguiente captura de pantalla. Los pasos para crear el concentrador IoT a través de este método y el catálogo de soluciones son idénticos.

- Creación de un concentrador IoT mediante el catálogo de soluciones: haciendo clic en **crear** abre un módulo que es idéntico a la hoja anterior para obtener una experiencia **+ nuevo** . Las siguientes secciones de la lista los distintos pasos en la creación de un concentrador IoT.

### <a name="choose-the-name-of-the-iot-hub"></a>Elija el nombre del concentrador IoT

Para crear un concentrador IoT, debe asignar un nombre al concentrador. Este nombre debe ser único en los centros. Duplicación de hubs no está permitido en el back-end, por lo que se recomienda que este concentrador se denomina como forma única como sea posible.

### <a name="choose-the-pricing-tier"></a>Elija el nivel de precios

Puede elegir entre cuatro niveles: **libre**, **estándar 1** y **2 estándar**y **S3 estándar**. El nivel gratuito permite sólo 500 dispositivos que estar conectado al concentrador IoT y hasta 8.000 mensajes al día.

**S1 estándar**: IoT Hubs S1 edition está diseñado para soluciones de IoT que tienen una gran cantidad de dispositivos generando relativamente pequeñas cantidades de datos por dispositivo. Cada unidad de la edición de S1 permite hasta 400.000 mensajes al día a través de todos los dispositivos conectados.

**S2 estándar**: IoT concentrador S2 edition está diseñado para soluciones de IoT en el que los dispositivos generan grandes cantidades de datos. Cada unidad de la edición de S2 permite hasta 6 millones de mensajes al día entre todos los dispositivos conectados.

**S3 estándar**: IoT concentrador S3 edition está diseñado para soluciones de IoT que generan grandes cantidades de datos. Cada unidad de la edición de S3 permite hasta 300 millones de mensajes al día entre todos los dispositivos conectados.

![][4]

> [AZURE.NOTE] Concentrador IoT solo permite a un concentrador gratuito por suscripción Azure.

### <a name="iot-hub-units"></a>Unidades de concentrador IoT

Una unidad de concentrador IoT incluye un número determinado de mensajes al día. El número total de mensajes compatibles para este concentrador es el número de unidades multiplicado por el número de mensajes por día para ese nivel. Por ejemplo, si desea que el concentrador IoT para admitir la entrada de 700.000 mensajes, elija dos unidades de nivel de S1.

### <a name="device-to-cloud-partitions-and-resource-group"></a>Dispositivo nube particiones y grupo de recursos

Puede cambiar el número de particiones para un concentrador IoT. Particiones predeterminadas se establecen en 4; Sin embargo, puede elegir un número diferente de particiones de una lista desplegable.

Para los grupos de recursos, es necesario crear explícitamente un grupo de recursos vacío. Al crear un recurso, puede crear un nuevo grupo de recursos o usar un grupo de recursos existente.

![][5]

### <a name="choose-subscriptions"></a>Elija suscripciones

Hub de Azure IoT automáticamente muestra la lista de suscripciones de Azure al que está vinculada a la cuenta de usuario. Puede elegir una de las opciones aquí para asociar el concentrador IoT suscripción Azure.

### <a name="choose-the-location"></a>Elija la ubicación

La opción de ubicación proporciona una lista de las regiones que se ofrezca IoT concentrador. Concentrador IoT está disponible para implementar en las siguientes ubicaciones: Australia Oriental, Australia sureste, Asia oriental, sudeste de Asia, Norte Europa, oeste de Europa, Oriente Japón, oeste Japón nos este, nos oeste.

### <a name="create-the-iot-hub"></a>Crear el concentrador IoT

Una vez completados todos los pasos anteriores, el concentrador IoT está listo para crear. Haga clic en **crear** para iniciar el proceso de back-end de creación de este concentrador IoT con las opciones específicas y para implementar a la ubicación especificada.

Este proceso puede demorarse unos minutos para que el concentrador IoT crearse como tiempo para la implementación de back-end que se produzca en los servidores de la ubicación apropiada.

## <a name="change-the-settings-of-the-iot-hub"></a>Cambiar la configuración del concentrador IoT

Puede cambiar la configuración de un concentrador IoT existente después de crearlo desde el módulo IoT concentrador.

![][8]

**Compartir directivas de acceso**: estas directivas definen los permisos para dispositivos y servicios para conectarse a IoT concentrador. Puede acceder a estas directivas haciendo clic en **Compartir directivas de acceso** en **General**. En este módulo, puede modificar las directivas existentes o agregar una nueva directiva.

### <a name="create-a-policy"></a>Crear una directiva

- Haga clic en **Agregar** para abrir un módulo. Introduzca el nuevo nombre de directiva y los permisos que desee asociar a esta directiva, tal como se muestra en la siguiente ilustración.

    Existen varios de los permisos que se pueden asociadas con estas directivas compartidas. Las dos primeras directivas, **registro de lectura** y **escritura de registro**, conceder leer y derechos de acceso de escritura a la tienda de identidad de dispositivo o el registro de la identidad. Si elige la opción de escritura automáticamente, elige la opción de lectura también.

    La directiva de **servicio conectar** concede permiso para tener acceso a los extremos del lado de la nube, como el grupo de consumidores para los servicios que se conecta al concentrador IoT. La directiva de **conectar el dispositivo** concede permisos de enviar y recibir mensajes en los extremos del dispositivo del concentrador IoT.

- Haga clic en **crear** para agregar el recién creado directiva a la lista existente.

![][10]

## <a name="messaging"></a>Mensajería

Haga clic en **mensajería** para mostrar una lista de propiedades para el concentrador IoT que se modifica la mensajería. Existen dos tipos principales de propiedades que puede modificar o copiar: **nube al dispositivo** y el **dispositivo en la nube**.

- Configuración de la **nube a dispositivo** : esta opción tiene dos subsettings: **nube dispositivo TTL** (time to live) y el **tiempo de retención** para los mensajes. Cuando se crea por primera vez el concentrador IoT, estas opciones de configuración se crean con un valor predeterminado de una hora. Para ajustar estos valores, use los controles deslizantes o escriba los valores.

- Configuración de **dispositivo en la nube** : esta configuración tiene varias subsettings, algunos de los cuales se denominado/asignan cuando el concentrador IoT se crea y solo se puede copiar en otros subsettings personalizables. Estos valores se muestran en la siguiente sección.

**Particiones**: se establece este valor cuando el concentrador IoT se crea y se puede cambiar mediante esta configuración.

**Punto final y el nombre de evento concentrador compatible**: se crea al IoT el concentrador, un concentrador de eventos se crea internamente que puede que tenga acceso a en determinadas circunstancias. Este nombre de evento concentrador compatible y un extremo no se puede personalizar, pero está disponibles para su uso mediante el botón **Copiar** .

**Tiempo de retención**: establezca en un día de forma predeterminada, pero se pueden personalizar otros valores de la lista desplegable. Este valor es en días de dispositivo en la nube y no en horas, como la configuración de similar de nube al dispositivo.

**Grupos de consumidores**: los grupos de consumidor son un valor similar a otros sistemas de mensajería que se pueden usar para extraer datos de formas específicas para conectarse a otras aplicaciones o servicios a IoT concentrador. Cada concentrador IoT se crea con un grupo de consumidores de forma predeterminada. Sin embargo, puede agregar o eliminar grupos de consumidores a su hubs IoT.

> [AZURE.NOTE] El grupo de consumidor predeterminado no se pueden modificar ni eliminar.

![][11]

## <a name="pricing-and-scale"></a>Precios y escala

El precio de un concentrador IoT existente se puede cambiar mediante la configuración de **precios** , con las excepciones siguientes:

- En la implementación actual, un concentrador IoT con un SKU gratuita no puede cambiar niveles en una de las SKU de pagadas, o viceversa.
- Solo puede haber un concentrador de IoT nivel gratuita en la suscripción de Azure.

![][12]

Pasar de un nivel superior (S2 o S3) Disminuir nivel (S1 o S2) sólo se permite cuando el número de mensajes enviados por ese día no está en conflicto. Por ejemplo, si el número de mensajes por día supera 400.000, puede cambiar el nivel de concentrador IoT. Sin embargo, si cambia al nivel S1 el concentrador está limitado para ese día.

## <a name="delete-the-iot-hub"></a>Eliminar el concentrador IoT

Puede examinar el concentrador IoT que desee eliminar haciendo clic en **Examinar**y seleccionando el concentrador adecuado para eliminar. Haga clic en el botón **Eliminar** debajo del nombre de concentrador para eliminar el concentrador.

## <a name="next-steps"></a>Pasos siguientes

Siga estos vínculos para obtener más información sobre la administración de Azure IoT concentrador:

- [Masa administrar dispositivos IoT][lnk-bulk]
- [Métrica de uso][lnk-metrics]
- [Operaciones de supervisión][lnk-monitor]

Para explorar aún más las capacidades de concentrador IoT, consulte:

- [Guía para programadores][lnk-devguide]
- [Simulación de un dispositivo con el SDK de puerta de enlace IoT][lnk-gateway]
- [Proteger la solución IoT desde el principio hacia arriba][lnk-securing]


  [4]: ./media/iot-hub-create-through-portal/create-iothub.png
  [5]: ./media/iot-hub-create-through-portal/location1.png
  [8]: ./media/iot-hub-create-through-portal/portal-settings.png
  [10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-create-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-create-through-portal/pricing-error.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md